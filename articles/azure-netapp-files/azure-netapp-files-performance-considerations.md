---
title: Prestatie overwegingen voor Azure NetApp Files | Microsoft Docs
description: Meer informatie over prestaties voor Azure NetApp Files, waaronder de relatie tussen quota en doorvoer limieten en hoe u volume quota dynamisch kunt verhogen of verlagen.
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
ms.openlocfilehash: 5f88b4755c7b4c0b20f27065cf9de2351251bc1c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513873"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Prestatie-overwegingen voor Azure NetApp Files

De [doorvoer limiet](azure-netapp-files-service-levels.md) voor een volume wordt bepaald door een combi natie van het quotum dat is toegewezen aan het volume en het geselecteerde service niveau. Wanneer u prestatie plannen maakt over Azure NetApp Files, moet u rekening houden met verschillende overwegingen. 

## <a name="quota-and-throughput"></a>Quota en door Voer  

De doorvoer limiet is slechts één determinant van de werkelijke prestaties die worden gerealiseerd.  

Typische overwegingen voor opslag prestaties, waaronder lees-en schrijf mix, de overdrachts grootte, wille keurige of sequentiële patronen en veel andere factoren dragen bij aan de totale prestaties die worden geleverd.  

De maximale empirische door Voer die is waargenomen in tests is 4.500 MiB/s.  In de laag Premium-opslag wordt met een volume quotum van 70,31 TiB een doorvoer limiet ingericht die hoog genoeg is om dit prestatie niveau te krijgen.  

Als u overweegt om volume quotum bedragen toe te wijzen groter dan 70,31 TiB, kan extra quotum worden toegewezen aan een volume voor het opslaan van aanvullende gegevens. Het toegevoegde quotum resulteert echter niet in een verdere toename van de werkelijke door voer.  

## <a name="overprovisioning-the-volume-quota"></a>Het volume quotum overtreffen

Als de prestaties van een werk belasting afhankelijk zijn van de doorvoer limiet, is het mogelijk om de volume quota te overbelasten om een hoger doorvoer niveau in te stellen en hogere prestaties te leveren.  

Als een volume in de laag voor Premium-opslag bijvoorbeeld slechts 500 GiB aan gegevens bevat, maar 128 MiB/s van door Voer vereist is, kunt u het quotum instellen op 2 TiB zodat het doorvoer niveau dienovereenkomstig wordt ingesteld (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Als u een volume voor een hogere door Voer consistent overneemt, overweeg dan het gebruik van een hoger service niveau.  In het bovenstaande voor beeld kunt u dezelfde doorvoer limiet bereikt met de helft van het volume quotum door gebruik te maken van de ultra Storage-laag, in plaats daarvan (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Volume quota dynamisch verg Roten of verkleinen

Als uw prestatie vereisten tijdelijk zijn, of als u de prestatie behoeften voor een bepaalde periode hebt verbeterd, kunt u het volume quotum dynamisch verg Roten of verkleinen om de doorvoer limiet direct aan te passen.  Houd rekening met de volgende overwegingen: 

* Volume quota kunnen worden verg root of verkleind zonder dat er IO hoeft te worden onderbroken en de toegang tot het volume wordt niet onderbroken of beïnvloed.  

    U kunt het quotum aanpassen tijdens een actieve I/O-trans actie op basis van een volume.  Houd er rekening mee dat volume quota nooit kleiner kunnen zijn dan de hoeveelheid logische gegevens die in het volume worden opgeslagen.

* Wanneer het volume quotum wordt gewijzigd, is de overeenkomstige wijziging in de doorvoer limiet bijna onmiddellijk. 

    De wijziging wordt niet onderbroken of heeft geen invloed op de toegang tot het volume of I/O.  

* Voor het aanpassen van het volume quotum is een wijziging in de grootte van de capaciteits groep vereist.  

    De grootte van de capaciteits pool kan dynamisch worden aangepast en zonder invloed op de beschik baarheid van volumes of I/O.

## <a name="next-steps"></a>Volgende stappen

- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestatiebenchmarks voor Linux](performance-benchmarks-linux.md)