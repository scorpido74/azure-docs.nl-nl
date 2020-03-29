---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 release notes| Microsoft Documenten
description: Beschrijft kritieke open problemen en oplossingen voor de StorSimple Virtual Array met Update 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420601"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1.2 release notes StorSimple Virtual Array Update 1.2 release notes StorSimple Virtual Array Update 1.2 release notes Stor

In de volgende releasenotes worden de kritieke open problemen en de opgeloste problemen voor Microsoft Azure StorSimple Virtual Array-updates geïdentificeerd.

De release notes worden voortdurend bijgewerkt. Wanneer er kritieke problemen worden ontdekt waarvoor een tijdelijke oplossing nodig is, wordt hierover informatie toegevoegd. Voordat u uw StorSimple Virtual Array implementeert, controleert u zorgvuldig de informatie in de releasenotes.

Update 1.2 komt overeen met de softwareversie **10.0.10311.0**.

> [!IMPORTANT]
> - Updates zijn storend en starten uw apparaat opnieuw op. Als I/O aan de gang is, wordt het apparaat uitval. Ga naar [Update 1.2 downloaden](#download-update-12)voor gedetailleerde instructies over pakketten die worden gebruikt om deze update toe te passen.
> - Update 1.2 is alleen beschikbaar via de Azure-portal als uw apparaat Update 1.0 of 1.1 uitvoert.

## <a name="whats-new-in-update-12"></a>Nieuwe informatie in Update 1.2

Deze update bevat de volgende bugfixes:

- Verbeterde tolerantie bij het verwerken van verwijderde bestanden.
- Verbeterde afhandelingsuitzonderingen in het opstartpad van de code, wat leidt tot minder fouten in back-ups, herstel, cloudreads en geautomatiseerde ruimteterugwinning.

## <a name="download-update-12"></a>Update 1.2 downloaden

Als u deze update wilt downloaden, gaat u naar de server van de [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) en downloadt u het KB4502035-pakket. Dit pakket bevat de volgende pakketten:

 - **KB4493446** met cumulatieve Windows-updates voor 2012 R2 tot april 2019. Voor meer informatie over wat is opgenomen in deze rollup, ga naar [april maandelijkse beveiliging rollup](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** dat is een Microsoft Update Standalone Package bestand WindowsTH-KB3011067-x64. Dit bestand wordt gebruikt om de apparaatsoftware bij te werken.

Download KB4502035 en volg deze instructies om de update toe te [passen via de lokale web-gebruikersinterface.](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="issues-fixed-in-update-12"></a>Problemen opgelost in Update 1.2

In de volgende tabel vindt u een overzicht van de problemen die in deze versie zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Verwijdering| In de vorige versies van de software was er een probleem toen het gebruik van het apparaat niet veranderde, zelfs niet wanneer bestanden werden verwijderd. Dit probleem is opgelost in deze versie. Tiering code pad werd veerkrachtiger gemaakt bij het verwerken van verwijderde bestanden.|
| 2 |Afhandeling van uitzonderingen| In de vorige versies van de software was er een probleem na de herstart van het systeem dat mogelijk kan leiden tot fouten in back-ups, herstel, lezen vanuit de cloud en geautomatiseerde ruimteterugwinning. Deze release bevat wijzigingen in de manier waarop de uitzonderingen zijn verwerkt in het opstartpad.|

## <a name="known-issues-in-update-12"></a>Bekende problemen in Update 1.2

Er zijn geen nieuwe problemen opgemerkt in Update 1.2. Alle release-opgemerkte problemen worden overgedragen van eerdere releases. Als u de samenvatting van bekende problemen uit de vorige versies wilt bekijken, gaat u naar [Bekende problemen in Update 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Volgende stappen

Download KB4502035 en [Pas de update toe via de lokale webgebruikersinterface.](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="references"></a>Verwijzingen

Op zoek naar een oudere release note? Ga naar:
* [StorSimple Virtual Array Update 1.1 Release Notes](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 Release Notes](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Release Notes](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Release Notes](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Release Notes](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Release Notes](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 en 0.2 Release Notes](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array Algemene Beschikbaarheid Release Notes](storsimple-ova-pp-release-notes.md)
