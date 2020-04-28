---
title: Systeem limieten voor StorSimple 8000-Series | Microsoft Docs
description: Beschrijft systeem limieten en aanbevolen grootten voor StorSimple-onderdelen en-verbindingen van de 8000-serie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68963378"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Wat zijn systeem limieten van de StorSimple 8000-reeks?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

StorSimple biedt schaal bare en flexibele opslag voor uw Data Center. Er zijn echter enkele beperkingen waarmee u rekening moet houden bij het plannen, implementeren en gebruiken van uw StorSimple-oplossing. In de volgende tabel worden deze limieten beschreven en worden enkele aanbevelingen gedaan, zodat u optimaal gebruik kunt maken van uw StorSimple-oplossing.

| Limiet-id | Limiet | Opmerkingen |
| --- | --- | --- |
| Maximum aantal referenties voor het opslag account |64 | |
| Maximum aantal volume containers |64 | |
| Maximum aantal volumes |255 | |
| Maximum aantal lokaal vastgemaakte volumes |32 | |
| Maximum aantal planningen per band breedte sjabloon |168 |Een planning voor elk uur, elke dag van de week (24 * 7). |
| Maximale grootte van een gelaagd volume op fysieke apparaten |64 TB voor 8100 en 8600 |8100 en 8600 zijn fysieke apparaten. |
| Maximale grootte van een gelaagd volume op virtuele apparaten in azure |30 TB voor 8010 <br></br> 64 TB voor 8020 |8010 en 8020 zijn virtuele apparaten in azure die respectievelijk gebruikmaken van standaard opslag en Premium Storage. |
| Maximale grootte van lokaal vastgemaakt volume op fysieke apparaten |8,5 TB voor 8100 <br></br> 22,5 TB voor 8600 |8100 en 8600 zijn fysieke apparaten. |
| Maximum aantal iSCSI-verbindingen |512 | |
| Maximum aantal iSCSI-verbindingen van initia tors |512 | |
| Maximum aantal toegangs beheer records per apparaat |64 | |
| Maximum aantal volumes per back-upbeleid |20 | |
| Maximum aantal back-ups dat per schema wordt bewaard (in een back-upbeleid) |64 | |
| Maximum aantal planningen per back-upbeleid |10 | |
| Maximum aantal moment opnamen van elk type dat per volume kan worden bewaard |256 |Dit aantal bevat lokale moment opnamen en Cloud momentopnamen. |
| Het maximum aantal moment opnamen dat aanwezig kan zijn op elk apparaat |10.000 | |
| Maximum aantal volumes dat parallel kan worden verwerkt voor back-up, herstel of kloon |16 |<ul><li>Als er meer dan 16 volumes zijn, worden ze opeenvolgend verwerkt omdat er verwerkings sleuven beschikbaar zijn.</li><li>Nieuwe back-ups van een gekloond of een hersteld gelaagd volume kunnen niet worden uitgevoerd totdat de bewerking is voltooid. Voor een lokaal volume zijn back-ups echter toegestaan nadat het volume online is.</li></ul> |
| Herstel tijd herstellen en klonen voor gelaagde volumes |< 2 minuten |<ul><li>Het volume wordt beschikbaar gesteld binnen twee minuten na het terugzetten of klonen, ongeacht de grootte van het volume.</li><li>De prestaties van het volume zijn mogelijk langzamer dan normaal, omdat de meeste gegevens en meta gegevens zich nog steeds in de cloud bevinden. Prestaties kunnen toenemen naarmate gegevens stromen van de Cloud naar het StorSimple-apparaat worden gestuurd.</li><li>De totale tijd voor het downloaden van meta gegevens is afhankelijk van de grootte van het toegewezen volume. Meta gegevens worden op de achtergrond automatisch op het apparaat gebracht, met een snelheid van vijf minuten per TB aan toegewezen volume gegevens. Dit aantal kan worden beïnvloed door de Internet bandbreedte voor de Cloud.</li><li>De herstel-of kloon bewerking is voltooid wanneer alle meta gegevens zich op het apparaat bevinden.</li><li>Er kunnen geen back-upbewerkingen worden uitgevoerd tot het terugzetten of de kloon is voltooid. |
| Herstel tijd herstellen voor lokaal vastgemaakte volumes |< 2 minuten |<ul><li>Het volume wordt beschikbaar gesteld binnen twee minuten nadat de herstel bewerking is uitgevoerd, ongeacht de grootte van het volume.</li><li>De prestaties van het volume zijn mogelijk langzamer dan normaal, omdat de meeste gegevens en meta gegevens zich nog steeds in de cloud bevinden. Prestaties kunnen toenemen naarmate gegevens stromen van de Cloud naar het StorSimple-apparaat worden gestuurd.</li><li>De totale tijd voor het downloaden van meta gegevens is afhankelijk van de grootte van het toegewezen volume. Meta gegevens worden op de achtergrond automatisch op het apparaat gebracht, met een snelheid van vijf minuten per TB aan toegewezen volume gegevens. Dit aantal kan worden beïnvloed door de Internet bandbreedte voor de Cloud.</li><li>In tegens telling tot gelaagde volumes, voor lokaal vastgemaakte volumes, worden de volume gegevens ook lokaal op het apparaat gedownload. De herstel bewerking is voltooid wanneer alle volume gegevens naar het apparaat zijn overgebracht.</li><li>De herstel bewerkingen kunnen lang zijn. De totale tijd voor het volt ooien van de herstel bewerking is afhankelijk van de grootte van het ingerichte lokale volume, uw Internet bandbreedte en de bestaande gegevens op het apparaat. Back-upbewerkingen op het lokaal vastgemaakte volume zijn toegestaan tijdens het terugzetten. |
| Verwerkings frequentie voor Cloud momentopnamen |15 minuten/TB |<ul><li>Minimale tijd om de Cloud momentopname gereed te maken voor upload, per TB toegewezen volume gegevens in een back-up. </li><li> De totale tijd voor de moment opname van de Cloud wordt berekend door deze tijd toe te voegen aan de upload tijd van de moment opname, die wordt beïnvloed door de Internet bandbreedte voor de Cloud. |
| Maximale door Voer voor lezen/schrijven van client (wanneer dit wordt bediend vanuit de SSD-laag) * |920/720 MB/s met één 10 GbE-netwerk interface |Tot twee keer met MPIO en twee netwerk interfaces. |
| Maximale door Voer voor lezen/schrijven van client (wanneer dit wordt bediend vanuit de HDD-laag) * |120/250 MB/s | |
| Maximale door Voer voor lezen/schrijven van client (wanneer dit wordt bediend vanuit de Cloud-laag) * voor update 3 en hoger * * |40/60 MB/s voor gelaagde volumes<br><br>60/80 MB/s voor gelaagde volumes met de optie archivering geselecteerd tijdens het maken van het volume |Lees doorvoer is afhankelijk van clients die voldoende I/O-wachtrij diepte genereren en onderhouden. <br><br>Snelheid bereikt is afhankelijk van de snelheid van het gebruikte onderliggende opslag account. |

&#42; maximale door Voer per I/O-type werd gemeten met 100 procent Lees-en 100 procent schrijf scenario's. De werkelijke door Voer is mogelijk lager en is afhankelijk van I/O-mix en netwerk omstandigheden.

&#42;&#42; prestatie aantallen voorafgaand aan update 3 kunnen lager zijn.

## <a name="next-steps"></a>Volgende stappen
Bekijk de [systeem vereisten voor StorSimple](storsimple-8000-system-requirements.md).

