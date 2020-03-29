---
title: StorSimple Virtual Array Update 0.4 release notes| Microsoft Documenten
description: Beschrijft kritieke open problemen en oplossingen voor de StorSimple Virtual Array met Update 0.4.
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
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 06a3469507631d032535bce62b01d964e99dc603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60334791"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0.4 release notes StorSimple Virtual Array Update 0.4 release notes StorSimple Virtual Array Update 0.4 release notes Stor

## <a name="overview"></a>Overzicht

In de volgende releasenotes worden de kritieke open problemen en de opgeloste problemen voor Microsoft Azure StorSimple Virtual Array-updates geïdentificeerd.

De release notes worden voortdurend bijgewerkt en als kritieke problemen die een tijdelijke oplossing vereisen worden ontdekt, worden ze toegevoegd. Voordat u uw StorSimple Virtual Array implementeert, controleert u zorgvuldig de informatie in de releasenotes.

Update 0.4 komt overeen met de softwareversie **10.0.10289.0**.

> [!NOTE]
> Updates zijn storend en starten uw apparaat opnieuw op. Als I/O aan de gang is, wordt het apparaat uitval.


## <a name="whats-new-in-the-update-04"></a>Nieuwe informatie in de update 0.4
Update 0.4 is voornamelijk een bug-fix build in combinatie met een paar verbeteringen. In deze versie zijn verschillende bugs die resulteren in back-upfouten in de vorige versie verholpen. De belangrijkste verbeteringen en bug-fixes zijn als volgt:

- **Verbeteringen in back-upprestaties** - Deze release heeft een aantal belangrijke verbeteringen aangebracht om de back-upprestaties te verbeteren. Als gevolg hiervan zien de back-ups die een groot aantal bestanden bevatten een aanzienlijke vermindering van de tijd die moet worden voltooid, voor volledige en incrementele back-ups.

- **Verbeterde herstelprestaties** - Deze release bevat verbeteringen die de herstelprestaties aanzienlijk verbeteren bij het gebruik van een groot aantal bestanden. Als u 2 - 4 miljoen bestanden gebruikt, raden we u aan een virtuele array met 16 GB RAM in te richten om de verbeteringen te zien. Bij het gebruik van minder dan 2 miljoen bestanden blijft de minimale vereiste voor de virtuele machine 8 GB RAM.

- **Verbeteringen in het ondersteuningspakket** - De verbeteringen omvatten het inloggen in de statistieken voor schijf, CPU, geheugen, netwerk en cloud in het Ondersteuningspakket, waardoor het proces van het diagnosticeren/debuggen van apparaatproblemen wordt verbeterd.

- **Beperk lokaal vastgemaakte iSCSI-volumes tot 200 GB** - Voor lokaal vastgemaakte volumes raden we u aan zich te beperken tot een iSCSI-volume van 200 GB op uw StorSimple Virtual Array. De lokale reservering voor gelaagde volumes blijft 10 % van de ingerichte volumegrootte, maar is beperkt tot 200 GB. 

- **Back-up-gerelateerde bug fixes** - In eerdere versies van software, waren er problemen met betrekking tot back-ups die back-upfouten zou veroorzaken. Deze bugs zijn aangepakt in deze release.


## <a name="issues-fixed-in-the-update-04"></a>Problemen opgelost in de update 0.4

In de volgende tabel vindt u een overzicht van de problemen die in deze versie zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Back-upprestaties|In de eerdere releases, de back-ups waarbij een groot aantal bestanden zou een lange tijd in beslag nemen (in de volgorde van dagen). In deze release, zowel de volledige en incrementele back-ups zien een aanzienlijke vermindering van de tijd tot voltooiing. |
| 2 |Ondersteuningspakket|Schijf-, CPU-, geheugen-, netwerk- en cloudstatistieken zijn nu aangemeld bij de ondersteuningslogboeken, waardoor de ondersteuningspakketten zeer effectief zijn bij het oplossen van apparaatproblemen.|
| 3 |Back-up |In eerdere versies kunnen langlopende back-ups leiden tot een ruimtecrunch op het apparaat, wat kan leiden tot back-upfouten. Deze bug wordt in deze release verholpen door niet meer dan 5 back-ups tegelijk in de wachtrij te laten staan.|
| 4 |iSCSI | In eerdere versies was de lokale reservering voor gelaagde of lokaal vastgemaakte volumes 10% van de ingerichte volumegrootte. In deze release is de lokale reservering voor alle iSCSI-volumes (lokaal vastgemaakt of gelaagd) beperkt tot 10 % met een maximum van maximaal 200 GB (voor gelaagde volumes groter dan 2 TB) waardoor meer ruimte op de lokale schijf vrijkomt. We raden u aan de lokaal vastgemaakte volumes in deze release te beperken tot 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Bekende problemen in de update 0.4

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
| **8.** |Gebruikte capaciteit voor aandelen |Mogelijk ziet u het verbruik van delen wanneer er geen gegevens over het aandeel zijn. Dit komt omdat de gebruikte capaciteit voor aandelen metadata bevat. | |
| **9.** |Herstel na noodgeval |U het noodherstel van een bestandsserver alleen uitvoeren naar hetzelfde domein als dat van het bronapparaat. Noodherstel naar een doelapparaat in een ander domein wordt niet ondersteund in deze release. |Dit wordt geïmplementeerd in een latere release. |
| **10.** |Azure PowerShell |De virtuele StorSimple-apparaten kunnen in deze versie niet worden beheerd via de Azure PowerShell. |Al het beheer van de virtuele apparaten moet worden gedaan via de Azure classic portal en de lokale web-ui. |
| **11.** |Wachtwoord wijzigen |De console van het virtual array-apparaat accepteert alleen invoer in de en-AMERIKAANSE toetsenbordindeling. | |
| **12.** |CHAP |CHAP-referenties die eenmaal zijn gemaakt, kunnen niet worden verwijderd. Bovendien, als u de CHAP-referenties wijzigt, moet u de volumes offline halen en ze vervolgens online brengen voordat de wijziging van kracht wordt. |Dit probleem wordt in een latere release verholpen. |
| **13.** |iSCSI-server |De 'Gebruikte opslag' die wordt weergegeven voor een iSCSI-volume kan verschillen in de StorSimple Manager-service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteemweergave.<br></br>Het apparaat ziet de blokken toegewezen wanneer het volume was op de maximale grootte. |
| **14.** |Bestandsserver |Als aan een bestand in een map een alternatieve gegevensstroom (ADS) is gekoppeld, wordt er geen back-up van het ADS gemaakt of hersteld via herstel, kloonen en Itemlevel Recovery. | |
| **15.** |Bestandsserver |Symbolische links worden niet ondersteund. | |
| **16.** |Bestandsserver |Bestanden die worden beschermd door EfS (Windows Encrypting File System) wanneer ze worden gekopieerd of opgeslagen op de StorSimple Virtual Array-bestandsserver, resulteren in een niet-ondersteunde configuratie.  | |

## <a name="next-step"></a>Volgende stap
[Installeer Update 0.4](storsimple-virtual-array-install-update-04.md) op uw StorSimple Virtual Array.

## <a name="references"></a>Verwijzingen
Op zoek naar een oudere release note? Ga naar: 

* [StorSimple Virtual Array Update 0.3 Release Notes](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 en 0.2 Release Notes](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array Algemene Beschikbaarheid Release Notes](storsimple-ova-pp-release-notes.md)

