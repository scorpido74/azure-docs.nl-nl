---
title: StorSimple 8000-serie systeemlimieten | Microsoft Documenten
description: Beschrijft systeemlimieten en aanbevolen formaten voor storSimple 8000-serie componenten en verbindingen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68963378"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Wat zijn storSimple 8000-serie systeemlimieten?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

StorSimple biedt schaalbare en flexibele opslag voor uw datacenter. Er zijn echter enkele limieten die u in gedachten moet houden bij het plannen, implementeren en bedienen van uw StorSimple-oplossing. In de volgende tabel worden deze limieten beschreven en worden enkele aanbevelingen gedaan, zodat u het meeste uit uw StorSimple-oplossing halen.

| Limiet-id | Limiet | Opmerkingen |
| --- | --- | --- |
| Maximaal aantal referenties voor opslagaccount |64 | |
| Maximum aantal volumecontainers |64 | |
| Maximum aantal volumes |255 | |
| Maximum aantal lokaal vastgemaakte volumes |32 | |
| Maximaal aantal schema's per bandbreedtesjabloon |168 |Een schema voor elk uur, elke dag van de week (24*7). |
| Maximale grootte van een gelaagd volume op fysieke apparaten |64 TB voor 8100 en 8600 |8100 en 8600 zijn fysieke apparaten. |
| Maximale grootte van een gelaagd volume op virtuele apparaten in Azure |30 TB voor 8010 <br></br> 64 TB voor 8020 |8010 en 8020 zijn virtuele apparaten in Azure die respectievelijk gebruik maken van standaardopslag en Premium-opslag. |
| Maximale grootte van een lokaal vastgemaakt volume op fysieke apparaten |8,5 TB voor 8100 <br></br> 22,5 TB voor 8600 |8100 en 8600 zijn fysieke apparaten. |
| Maximum aantal iSCSI-verbindingen |512 | |
| Maximum aantal iSCSI-verbindingen van initiators |512 | |
| Maximum aantal toegangscontrolerecords per apparaat |64 | |
| Maximaal aantal volumes per back-upbeleid |20 | |
| Maximaal aantal back-ups dat per schema wordt bewaard (in een back-upbeleid) |64 | |
| Maximaal aantal schema's per back-upbeleid |10 | |
| Maximaal aantal momentopnamen van elk type dat per volume kan worden bewaard |256 |Dit nummer bevat lokale momentopnamen en cloudsnapshots. |
| Maximaal aantal momentopnamen dat in elk apparaat aanwezig kan zijn |10.000 | |
| Maximaal aantal volumes dat parallel kan worden verwerkt voor back-up, herstel of kloon |16 |<ul><li>Als er meer dan 16 volumes zijn, worden ze opeenvolgend verwerkt als verwerkingssleuven beschikbaar komen.</li><li>Nieuwe back-ups van een gekloond of een hersteld gelaagd volume kunnen niet plaatsvinden totdat de bewerking is voltooid. Voor een lokaal volume zijn back-ups echter toegestaan nadat het volume online is.</li></ul> |
| Hersteltijd herstellen en klonen voor gelaagde volumes |< 2 minuten |<ul><li>Het volume wordt beschikbaar gesteld binnen 2 minuten na herstel of kloonbewerking, ongeacht de volumegrootte.</li><li>De volumeprestaties kunnen in eerste instantie trager zijn dan normaal, omdat de meeste gegevens en metagegevens zich nog steeds in de cloud bevinden. De prestaties kunnen toenemen naarmate gegevens van de cloud naar het StorSimple-apparaat stromen.</li><li>De totale tijd om metagegevens te downloaden is afhankelijk van de toegewezen volumegrootte. Metagegevens worden automatisch op de achtergrond in het apparaat gebracht met een snelheid van 5 minuten per TB aan toegewezen volumegegevens. Dit tarief kan worden beïnvloed door internetbandbreedte naar de cloud.</li><li>De herstel- of kloonbewerking is voltooid wanneer alle metagegevens op het apparaat staan.</li><li>Back-upbewerkingen kunnen niet worden uitgevoerd totdat de herstel- of kloonbewerking volledig is voltooid. |
| Hersteltijd herstellen voor lokaal vastgemaakte volumes |< 2 minuten |<ul><li>Het volume wordt beschikbaar gesteld binnen 2 minuten na de herstelbewerking, ongeacht de grootte van het volume.</li><li>De volumeprestaties kunnen in eerste instantie trager zijn dan normaal, omdat de meeste gegevens en metagegevens zich nog steeds in de cloud bevinden. De prestaties kunnen toenemen naarmate gegevens van de cloud naar het StorSimple-apparaat stromen.</li><li>De totale tijd om metagegevens te downloaden is afhankelijk van de toegewezen volumegrootte. Metagegevens worden automatisch op de achtergrond in het apparaat gebracht met een snelheid van 5 minuten per TB aan toegewezen volumegegevens. Dit tarief kan worden beïnvloed door internetbandbreedte naar de cloud.</li><li>In tegenstelling tot gelaagde volumes worden voor lokaal vastgemaakte volumes de volumegegevens ook lokaal op het apparaat gedownload. De herstelbewerking is voltooid wanneer alle volumegegevens naar het apparaat zijn gebracht.</li><li>De herstelbewerkingen kunnen lang zijn. De totale tijd om het herstel te voltooien is afhankelijk van de grootte van het ingerichte lokale volume, uw internetbandbreedte en de bestaande gegevens op het apparaat. Back-upbewerkingen op het lokaal vastgemaakte volume zijn toegestaan terwijl de herstelbewerking wordt uitgevoerd. |
| Verwerkingssnelheid voor cloudmomentopnamen |15 minuten/TB |<ul><li>Minimale tijd om de cloudmomentopname geüpload te maken, per TB aan toegewezen volumegegevens in back-up. </li><li> De totale momentopnametijd van de cloud wordt berekend door deze tijd toe te voegen aan de uploadtijd van momentopnamen, die wordt beïnvloed door de internetbandbreedte naar de cloud. |
| Maximale lees-/schrijfdoorvoer van de client (wanneer deze wordt bediend vanuit de SSD-laag)* |920/720 MB/s met één 10 GbE-netwerkinterface |Tot 2x met MPIO en twee netwerkinterfaces. |
| Maximale lees-/schrijfdoorvoer van de client (wanneer deze wordt bediend vanaf de HDD-laag)* |120/250 MB/s | |
| Maximale lees-/schrijfdoorvoer van de client (wanneer deze vanuit de cloudlaag wordt weergegeven)* voor Update 3 en hoger** |40/60 MB/s voor gelaagde volumes<br><br>60/80 MB/s voor gelaagde volumes met archiefoptie geselecteerd tijdens het maken van volume |Leesdoorvoer is afhankelijk van clients die voldoende I/O-wachtrijdiepte genereren en behouden. <br><br>De bereikte snelheid is afhankelijk van de snelheid van het onderliggende opslagaccount dat wordt gebruikt. |

&#42; Maximale doorvoer per I/O-type werd gemeten met 100 procent lees- en 100 procent schrijfscenario's. De werkelijke doorvoer kan lager zijn en is afhankelijk van de I/O-mix en netwerkomstandigheden.

&#42;&#42; Prestatienummers voorafgaand aan update 3 mogelijk lager zijn.

## <a name="next-steps"></a>Volgende stappen
Bekijk de [StorSimple-systeemvereisten.](storsimple-8000-system-requirements.md)

