---
title: StorSimple Virtual Array Update 0.5 release notes| Microsoft Documenten
description: Beschrijft kritieke open problemen en oplossingen voor de StorSimple Virtual Array met Update 0.5.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60870669"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array Update 0.5 release notes StorSimple Virtual Array Update 0.5 release notes StorSimple Virtual Array Update 0.5 release notes Stor

## <a name="overview"></a>Overzicht

In de volgende releasenotes worden de kritieke open problemen en de opgeloste problemen voor Microsoft Azure StorSimple Virtual Array-updates geïdentificeerd.

De release notes worden voortdurend bijgewerkt en als kritieke problemen die een tijdelijke oplossing vereisen worden ontdekt, worden ze toegevoegd. Voordat u uw StorSimple Virtual Array implementeert, controleert u zorgvuldig de informatie in de releasenotes.

Update 0.5 komt overeen met de softwareversie **10.0.10290.0**.

> [!NOTE]
> Updates zijn storend en starten uw apparaat opnieuw op. Als I/O aan de gang is, wordt het apparaat uitval. Ga naar [Update 0.5 installeren](storsimple-virtual-array-install-update-05.md)voor gedetailleerde instructies over het toepassen van de update.


## <a name="whats-new-in-the-update-05"></a>Nieuwe informatie in de update 0.5
Update 0.5 is voornamelijk een bug-fix build. De belangrijkste verbeteringen en bug-fixes zijn als volgt:

- **Verbeteringen in back-uptolerantie** - Deze release heeft oplossingen die de tolerantie voor back-ups verbeteren. In de eerdere versies werden back-ups alleen opnieuw geprobeerd voor bepaalde uitzonderingen. Deze release probeert alle back-up uitzonderingen en maakt de back-ups veerkrachtiger.

- **Updates voor opslaggebruiksbewaking** - Vanaf 30 juni 2017 wordt de opslagcontrole voor StorSimple Virtual Device Series buiten gebruik gesteld. Dit geldt voor de bewakingsdiagrammen op alle virtuele arrays met Update 0.4 of lager. Deze update bevat de wijzigingen die nodig zijn om het gebruik van opslaggebruiksbewaking in de Azure-portal voort te zetten. Installeer deze kritieke update vóór 30 juni 2017 om de bewakingsfunctie te blijven gebruiken.


## <a name="issues-fixed-in-the-update-05"></a>Problemen opgelost in de update 0.5

In de volgende tabel vindt u een overzicht van de problemen die in deze versie zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Reservetolerantie| In de eerdere versies werden back-ups alleen opnieuw geprobeerd voor bepaalde uitzonderingen. Deze release bevat een oplossing om back-ups veerkrachtiger te maken door alle back-upuitzonderingen opnieuw uit te proberen.|
| 2 |Bewaking| De opslaggebruiksbewaking voor StorSimple Virtual Device-serie wordt vanaf 30 juni 2017 afgeschaft. Deze actie heeft gevolgen voor de monitoringgrafieken van de StorSimple Device Manager-service die wordt uitgevoerd op StorSimple Virtual Arrays (1200-model). Deze release heeft updates waarmee de gebruiker het gebruik van opslaggebruikscontrole op de virtuele arrays na 30 juni 2017 kan voortzetten.|
| 3 |Bestandsserver| In de eerdere versies kan een gebruiker per ongeluk versleutelde bestanden kopiëren naar de virtuele array. Deze release bevat een oplossing die het kopiëren van versleutelde bestanden naar virtuele array niet mogelijk zou maken. Als uw apparaat voorafgaand aan de update bestaande versleutelde bestanden heeft, blijven back-ups mislukken totdat alle versleutelde bestanden van het systeem zijn verwijderd. |


## <a name="known-issues-in-the-update-05"></a>Bekende problemen in de update 0.5

De volgende tabel bevat een overzicht van bekende problemen voor de StorSimple Virtual Array en bevat de problemen release-opgemerkt van de vorige releases.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Updates |De virtuele apparaten die in de preview-versie zijn gemaakt, kunnen niet worden bijgewerkt naar een ondersteunde versie met algemene beschikbaarheid. |Deze virtuele apparaten moeten zijn mislukt voor de release algemene beschikbaarheid met behulp van een DR-werkstroom (Disaster Recovery). |
| **2.** |Ingerichte gegevensschijf |Zodra u een gegevensschijf van een bepaalde opgegeven grootte hebt ingericht en het bijbehorende virtuele StorSimple-apparaat hebt gemaakt, mag u de gegevensschijf niet uitbreiden of verkleinen. Een poging om te doen resulteert in een verlies van alle gegevens in de lokale lagen van het apparaat. | |
| **3.** |Groepsbeleid |Wanneer een apparaat is verbonden met een domein, kan het toepassen van een groepsbeleid een negatieve invloed hebben op de werking van het apparaat. |Controleer of uw virtuele array zich in de eigen organisatie-eenheid (OU) voor Active Directory bevindt en dat er geen groepsbeleidsobjecten (GPO) op worden toegepast. |
| **4.** |Lokale webgebruikersinterface |Als verbeterde beveiligingsfuncties zijn ingeschakeld in Internet Explorer (IE ESC), werken sommige lokale web-ui-pagina's zoals probleemoplossing of onderhoud mogelijk niet goed. Knoppen op deze pagina's kunnen ook niet werken. |Schakel verbeterde beveiligingsfuncties uit in Internet Explorer. |
| **5.** |Lokale webgebruikersinterface |In een Virtuele Hyper-V-machine worden de netwerkinterfaces in de web-gebruikersinterface weergegeven als 10 Gbps-interfaces. |Dit gedrag is een weerspiegeling van Hyper-V. Hyper-V toont altijd 10 Gbps voor virtuele netwerkadapters. |
| **6.** |Gelaagde volumes of aandelen |Byte-bereikvergrendeling voor toepassingen die werken met de gelaagde volumes van StorSimple, wordt niet ondersteund. Als byte-bereikvergrendeling is ingeschakeld, werkt StorSimple-laaglaag niet. |Aanbevolen maatregelen zijn onder meer: <br></br>Schakel de vergrendeling van het bytebereik in uw toepassingslogica uit.<br></br>Kies ervoor om gegevens voor deze toepassing in lokaal vastgemaakte volumes te plaatsen in tegenstelling tot gelaagde volumes.<br></br>*Waarschuwing*: Wanneer lokaal vastgemaakte volumes worden gebruikt en de vergrendeling van het bytebereik is ingeschakeld, kan het lokaal vastgemaakte volume online zijn, zelfs voordat het herstel is voltooid. In dergelijke gevallen, als een herstel aan de gang is, moet u wachten tot het herstel is voltooid. |
| **7.** |Gelaagde aandelen |Werken met grote bestanden kan resulteren in een langzame laag uit. |Bij het werken met grote bestanden raden we aan dat het grootste bestand kleiner is dan 3% van de grootte van het aandeel. |
| **8.** |Gebruikte capaciteit voor aandelen |Mogelijk ziet u het verbruik van delen wanneer er geen gegevens over het aandeel zijn. Dit verbruik is omdat de gebruikte capaciteit voor aandelen metadata bevat. | |
| **9.** |Herstel na noodgeval |U het noodherstel van een bestandsserver alleen uitvoeren naar hetzelfde domein als dat van het bronapparaat. Noodherstel naar een doelapparaat in een ander domein wordt niet ondersteund in deze release. |Dit wordt geïmplementeerd in een latere release. Ga voor meer informatie naar [Failover en disaster recovery voor uw StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |De virtuele StorSimple-apparaten kunnen in deze versie niet worden beheerd via de Azure PowerShell. |Al het beheer van de virtuele apparaten moet worden gedaan via de Azure-portal en de lokale web-gebruikersinterface. |
| **11.** |Wachtwoord wijzigen |De console van het virtual array-apparaat accepteert alleen invoer in en-us-toetsenbordindeling. | |
| **12.** |CHAP |CHAP-referenties die eenmaal zijn gemaakt, kunnen niet worden verwijderd. Bovendien, als u de CHAP-referenties wijzigt, moet u de volumes offline halen en ze vervolgens online brengen voordat de wijziging van kracht wordt. |Dit probleem wordt in een latere release verholpen. |
| **13.** |iSCSI-server |De 'Gebruikte opslag' die wordt weergegeven voor een iSCSI-volume kan verschillen in de StorSimple Device Manager-service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteemweergave.<br></br>Het apparaat ziet de blokken toegewezen wanneer het volume was op de maximale grootte. |
| **14.** |Bestandsserver |Als aan een bestand in een map een alternatieve gegevensstroom (ADS) is gekoppeld, wordt er geen back-up van het ADS gemaakt of hersteld via herstel, kloonen en Itemlevel Recovery. | |
| **15.** |Bestandsserver |Symbolische links worden niet ondersteund. | |
| **16.** |Bestandsserver |Bestanden die worden beschermd door EfS (Windows Encrypting File System) wanneer ze worden gekopieerd of opgeslagen op de StorSimple Virtual Array-bestandsserver, resulteren in een niet-ondersteunde configuratie.  | |

## <a name="next-step"></a>Volgende stap
[Installeer Update 0.5](storsimple-virtual-array-install-update-05.md) op uw StorSimple Virtual Array.

## <a name="references"></a>Verwijzingen
Op zoek naar een oudere release note? Ga naar:

* [StorSimple Virtual Array Update 0.4 Release Notes](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Release Notes](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 en 0.2 Release Notes](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array Algemene Beschikbaarheid Release Notes](storsimple-ova-pp-release-notes.md)

