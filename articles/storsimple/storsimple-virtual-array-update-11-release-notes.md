---
title: StorSimple Virtual Array Update 1.1 release notes| Microsoft Documenten
description: Beschrijft kritieke open problemen en oplossingen voor de StorSimple Virtual Array met Update 1.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: 55103d6307614f8796c41c35d6345e1fc3aca261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60789634"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>StorSimple Virtual Array Update 1.1 release notes StorSimple Virtual Array Update 1.1 release notes StorSimple Virtual Array Update 1.1 release notes Stor

## <a name="overview"></a>Overzicht

In de volgende releasenotes worden de kritieke open problemen en de opgeloste problemen voor Microsoft Azure StorSimple Virtual Array-updates geïdentificeerd.

De release notes worden voortdurend bijgewerkt en als kritieke problemen die een tijdelijke oplossing vereisen worden ontdekt, worden ze toegevoegd. Voordat u uw StorSimple Virtual Array implementeert, controleert u zorgvuldig de informatie in de releasenotes.

Update 1.1 komt overeen met de softwareversie **10.0.10307.0**.

> [!IMPORTANT]
> - Updates zijn storend en starten uw apparaat opnieuw op. Als I/O aan de gang is, wordt het apparaat uitval. Ga naar [Update 1.1 installeren](storsimple-virtual-array-install-update-11.md)voor gedetailleerde instructies over het toepassen van de update.
>
> - Update 1.1 is alleen beschikbaar via de Azure-portal als uw apparaat Update 1.0 uitvoert.

## <a name="whats-new-in-update-11"></a>Nieuwe informatie in Update 1.1

Deze update bevat de volgende verbetering sto;o.i.

 - **Back-upfouten** - zijn verbeterd door de tolerantie voor cloudfouten en een hoog CPU-verbruik te vergroten.
 - **Logboekregistratie** - Er zijn wijzigingen aangebracht in het logboeken van de verbose-modus wanneer het apparaat in de ondersteuningssessie staat.


## <a name="issues-fixed-in-update-11"></a>Problemen opgelost in Update 1.1

In de volgende tabel vindt u een overzicht van de problemen die in deze versie zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Back-ups| Deze release bevat wijzigingen die de back-upfouten hebben verbeterd door de tolerantie voor cloudfouten en een hoog CPU-gebruik te vergroten.|
| 2 |Logboekregistratie| Deze release bevat wijzigingen in logboekregistratie terwijl het apparaat in de ondersteuningssessie in de uitgebreide modus staat.|


## <a name="known-issues-in-update-11"></a>Bekende problemen in Update 1.1

De volgende tabel bevat een overzicht van bekende problemen voor de StorSimple Virtual Array en bevat de problemen release-opgemerkt van de vorige releases.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Updates |De virtuele arrays die in de preview-versie zijn gemaakt, kunnen niet worden bijgewerkt naar een ondersteunde versie met algemene beschikbaarheid. |Deze virtuele arrays moeten zijn mislukt voor de release algemene beschikbaarheid met behulp van een DR-werkstroom (Disaster Recovery). |
| **2.** |Ingerichte gegevensschijf |Zodra u een gegevensschijf van een bepaalde opgegeven grootte hebt ingericht en de bijbehorende StorSimple Virtual Array hebt gemaakt, mag u de gegevensschijf niet uitbreiden of verkleinen. Een poging om te doen resulteert in een verlies van alle gegevens in de lokale lagen van het apparaat. | |
| **3.** |Groepsbeleid |Wanneer een apparaat is verbonden met een domein, kan het toepassen van een groepsbeleid een negatieve invloed hebben op de werking van het apparaat. |Controleer of uw virtuele array zich in de eigen organisatie-eenheid (OU) voor Active Directory bevindt en dat er geen groepsbeleidsobjecten (GPO) op worden toegepast. |
| **4.** |Lokale webgebruikersinterface |Als verbeterde beveiligingsfuncties zijn ingeschakeld in Internet Explorer (IE ESC), werken sommige lokale web-ui-pagina's zoals probleemoplossing of onderhoud mogelijk niet goed. Knoppen op deze pagina's kunnen ook niet werken. |Schakel verbeterde beveiligingsfuncties uit in Internet Explorer. |
| **5.** |Lokale webgebruikersinterface |In een Virtuele Hyper-V-machine worden de netwerkinterfaces in de web-gebruikersinterface weergegeven als 10 Gbps-interfaces. |Dit gedrag is een weerspiegeling van Hyper-V. Hyper-V toont altijd 10 Gbps voor virtuele netwerkadapters. |
| **6.** |Gelaagde volumes of aandelen |Byte-bereikvergrendeling voor toepassingen die werken met de gelaagde volumes van StorSimple, wordt niet ondersteund. Als byte-bereikvergrendeling is ingeschakeld, werkt StorSimple-laaglaag niet. |Aanbevolen maatregelen zijn onder meer: <br></br>Schakel de vergrendeling van het bytebereik in uw toepassingslogica uit.<br></br>Kies ervoor om gegevens voor deze toepassing in lokaal vastgemaakte volumes te plaatsen in tegenstelling tot gelaagde volumes.<br></br>*Waarschuwing*: Wanneer lokaal vastgemaakte volumes worden gebruikt en de vergrendeling van het bytebereik is ingeschakeld, kan het lokaal vastgemaakte volume online zijn, zelfs voordat het herstel is voltooid. In dergelijke gevallen, als een herstel aan de gang is, moet u wachten tot het herstel is voltooid. |
| **7.** |Gelaagde aandelen |Werken met grote bestanden kan resulteren in een langzame laag uit. |Bij het werken met grote bestanden raden we aan dat het grootste bestand kleiner is dan 3% van de grootte van het aandeel. |
| **8.** |Gebruikte capaciteit voor aandelen |Mogelijk ziet u het verbruik van delen wanneer er geen gegevens over het aandeel zijn. Dit verbruik is omdat de gebruikte capaciteit voor aandelen metadata bevat. | |
| **9.** |Herstel na noodgeval |U het noodherstel van een bestandsserver alleen uitvoeren naar hetzelfde domein als dat van het bronapparaat. Noodherstel naar een doelapparaat in een ander domein wordt niet ondersteund in deze release. |Dit wordt geïmplementeerd in een latere release. Ga voor meer informatie naar [Failover en disaster recovery voor uw StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |De StorSimple Virtual Arrays kunnen in deze versie niet worden beheerd via de Azure PowerShell. |Al het beheer van de virtuele apparaten moet worden gedaan via de Azure-portal en de lokale web-gebruikersinterface. |
| **11.** |Wachtwoord wijzigen |De console van het virtual array-apparaat accepteert alleen invoer in en-us-toetsenbordindeling. | |
| **12.** |CHAP |CHAP-referenties die eenmaal zijn gemaakt, kunnen niet worden verwijderd. Bovendien, als u de CHAP-referenties wijzigt, moet u de volumes offline halen en ze vervolgens online brengen voordat de wijziging van kracht wordt. |Dit probleem wordt in een latere release verholpen. |
| **13.** |iSCSI-server |De 'Gebruikte opslag' die wordt weergegeven voor een iSCSI-volume kan verschillen in de StorSimple Device Manager-service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteemweergave.<br></br>Het apparaat ziet de blokken toegewezen wanneer het volume was op de maximale grootte. |
| **14.** |Bestandsserver |Als aan een bestand in een map een alternatieve gegevensstroom (ADS) is gekoppeld, wordt er geen back-up van het ADS gemaakt of hersteld via herstel, kloonen en Itemlevel Recovery. | |
| **15.** |Bestandsserver |Symbolische links worden niet ondersteund. | |
| **16.** |Bestandsserver |Bestanden die worden beschermd door EfS (Windows Encrypting File System) wanneer ze worden gekopieerd of opgeslagen op de StorSimple Virtual Array-bestandsserver, resulteren in een niet-ondersteunde configuratie.  | |
| **17.** |Updates |Als u foutcode ziet: 2359302 (hex 0x240006) wanneer u een hotfix probeert te installeren via de lokale gebruikersinterface, betekent dit dat de hotfix al op uw apparaat is geïnstalleerd.   | |
| **18.** |Updates |Als u de lokale webgebruikersinterface gebruikt om Update 1 op uw virtuele array te installeren, moet u ervoor zorgen dat u Update 0.6 uitvoert. Als u een versie uitvoert die lager is dan Update 0.6, moet u update 0.6 eerst installeren en vervolgens Update 1 toepassen. Als u Update 1.0 rechtstreeks installeert vanuit een pre-Update 0.6-versie, mist u enkele updates en werken de bewakingsschema's niet.   | |


## <a name="next-steps"></a>Volgende stappen
[Installeer Update 1.1](storsimple-virtual-array-install-update-11.md) op uw StorSimple Virtual Array.

## <a name="references"></a>Verwijzingen
Op zoek naar een oudere release note? Ga naar:
* [StorSimple Virtual Array Update 1.0 Release Notes](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Release Notes](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Release Notes](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Release Notes](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Release Notes](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 en 0.2 Release Notes](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array Algemene Beschikbaarheid Release Notes](storsimple-ova-pp-release-notes.md)
