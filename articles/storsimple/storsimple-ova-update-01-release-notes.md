---
title: StorSimple Virtual Array Update 0,2 & 0.1 release notes
description: Beschrijft kritieke open problemen en oplossingen voor de StorSimple Virtual Array met Update 0.2 en 0.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 95386f36340aca470769c920e40bbb70e09d34fc
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397871"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0.2 en 0.1 release notes StorSimple Virtual Array Update 0.2 en 0.1 release notes StorSimple Virtual Array Update 0.2 and 0.1 release notes Stor
## <a name="overview"></a>Overzicht
In de volgende releasenotes worden de kritieke open problemen en de opgeloste problemen voor Microsoft Azure StorSimple Virtual Array-updates geïdentificeerd. (Microsoft Azure StorSimple Virtual Array wordt ook wel het storSimple on-premises virtuele apparaat of het virtuele StorSimple-apparaat genoemd.) 

De release notes worden voortdurend bijgewerkt en als kritieke problemen die een tijdelijke oplossing vereisen worden ontdekt, worden ze toegevoegd. Voordat u uw StorSimple virtuele apparaat implementeert, zorgvuldig controleren van de informatie in de release notes.

Update 0.2 komt overeen met de softwareversie **10.0.10280.0**; Update 0.1 is versie **10.0.10279.0**. In de onderstaande secties worden de wijzigingen voor elke update vermeld. 

> [!NOTE]
> Updates zijn storend en starten uw apparaat opnieuw op. Als i/o aan de gang is, wordt het apparaat uitvaltijd.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemen opgelost in de update 0.2
Update 0.2 bevat alle wijzigingen ten opzichte van update 0.1 naast de oplossing die in de volgende tabel wordt beschreven:

| Functie | Probleem |
| --- | --- |
| Updates |In de laatste versie werden updates niet automatisch gedetecteerd in de klassieke Azure-portal, dus moest u de lokale webgebruikersinterface gebruiken om updates te installeren. Dit probleem is opgelost in deze release. Nadat u Update 0.2 hebt geïnstalleerd, u toekomstige updates installeren met behulp van de klassieke Azure-portal. |

## <a name="whats-new-in-the-update-01"></a>Nieuwe informatie in de update 0.1
Update 0.1 bevat de volgende bugfixes en verbeteringen. 

* **Verbeterde tolerantie voor cloudstoringen:** deze release heeft verschillende bugfixes rond noodherstel, back-up, herstel en gelaagdheid in het geval van een verstoring van de cloudverbinding. 
* **Verbeterde herstelprestaties**: Deze release heeft bugfixes die de voltooiingstijd van de hersteltaken aanzienlijk hebben verkort.
* **Optimalisatie van geautomatiseerde ruimteterugwinning**: Wanneer gegevens worden verwijderd op dun ingerichte volumes, moeten de ongebruikte opslagblokken worden teruggewonnen. Deze release heeft het ruimteterugwinningsproces van de cloud verbeterd, waardoor de ongebruikte ruimte sneller beschikbaar komt in vergelijking met de vorige versies.
* **Nieuwe virtuele schijfafbeeldingen:** Nieuwe VHD, VHDX en VMDK zijn nu beschikbaar via de klassieke Azure-portal. U deze afbeeldingen downloaden om nieuwe Update 0.1-apparaten in te richten.
* **De nauwkeurigheid van de status van taken in de portal verbeteren**: In de eerdere versie van software was de taakstatusrapportage in de portal niet gedetailleerd. Dit probleem is opgelost in deze release.
* **Domeinjoinervaring:** Bugfixes met betrekking tot het verbinden en hernoemen van het apparaat.

## <a name="issues-fixed-in-the-update-01"></a>Problemen opgelost in de update 0.1
In de volgende tabel vindt u een overzicht van de problemen die in deze versie zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Vmdk |In sommige VMware-versies werd de OS-schijf gezien als schaars waardoor waarschuwingen werden veroorzaakt en normale bewerkingen werden verstoord. Dit werd opgelost in deze release. |
| 2 |iSCSI-server |In de laatste release moest de gebruiker een gateway opgeven voor elke ingeschakelde netwerkinterface van uw StorSimple virtuele apparaat. Dit gedrag wordt in deze versie gewijzigd, zodat de gebruiker ten minste één gateway moet configureren voor alle ingeschakelde netwerkinterfaces. |
| 3 |Ondersteuningspakket |In de eerdere versie van software is het verzamelen van ondersteuningspakketten mislukt toen de pakketgroottes groter waren dan 1 GB. Dit probleem is opgelost in deze release. |
| 4 |Cloudtoegang |In de laatste versie, als de StorSimple Virtual Array geen netwerkconnectiviteit had en opnieuw werd opgestart, zou de lokale gebruikersinterface verbindingsproblemen hebben. Dit probleem is opgelost in deze release. |
| 5 |Grafieken controleren |In de vorige versie, na een failover van het apparaat, worden in de grafieken voor de gebruikscapaciteit van de cloudcapaciteit onjuiste waarden weergegeven in de klassieke Azure-portal. Dit is opgelost in de huidige release. |

## <a name="known-issues-in-the-update-01"></a>Bekende problemen in de update 0.1
De volgende tabel bevat een overzicht van bekende problemen voor de StorSimple Virtual Array en bevat de problemen release-opgemerkt van de vorige releases. **De problemen release opgemerkt in deze release zijn gemarkeerd met een sterretje. Bijna alle problemen in deze lijst hebben overgedragen van de GA release van StorSimple Virtual Array.**

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Updates |De virtuele apparaten die in de preview-versie zijn gemaakt, kunnen niet worden bijgewerkt naar een ondersteunde versie met algemene beschikbaarheid. |Deze virtuele apparaten moeten zijn mislukt voor de release algemene beschikbaarheid met behulp van een DR-werkstroom (Disaster Recovery). |
| **2.** |Ingerichte gegevensschijf |Zodra u een gegevensschijf van een bepaalde opgegeven grootte hebt ingericht en het bijbehorende virtuele StorSimple-apparaat hebt gemaakt, mag u de gegevensschijf niet uitbreiden of verkleinen. Als u dit probeert, worden alle gegevens in de lokale lagen van het apparaat verloren. | |
| **3.** |Groepsbeleid |Wanneer een apparaat is verbonden met een domein, kan het toepassen van een groepsbeleid een negatieve invloed hebben op de werking van het apparaat. |Controleer of uw virtuele array zich in de eigen organisatie-eenheid (OU) voor Active Directory bevindt en dat er geen groepsbeleidsobjecten (GPO) op worden toegepast. |
| **4.** |Lokale webgebruikersinterface |Als verbeterde beveiligingsfuncties zijn ingeschakeld in Internet Explorer (IE ESC), werken sommige lokale web-ui-pagina's zoals probleemoplossing of onderhoud mogelijk niet goed. Knoppen op deze pagina's kunnen ook niet werken. |Schakel verbeterde beveiligingsfuncties uit in Internet Explorer. |
| **5.** |Lokale webgebruikersinterface |In een Virtuele Hyper-V-machine worden de netwerkinterfaces in de web-gebruikersinterface weergegeven als 10 Gbps-interfaces. |Dit gedrag is een weerspiegeling van Hyper-V. Hyper-V toont altijd 10 Gbps voor virtuele netwerkadapters. |
| **6.** |Gelaagde volumes of aandelen |Byte-bereikvergrendeling voor toepassingen die werken met de gelaagde volumes van StorSimple, wordt niet ondersteund. Als byte-bereikvergrendeling is ingeschakeld, werkt StorSimple-laaglaagniet. |Aanbevolen maatregelen zijn onder meer: <br></br>Schakel de vergrendeling van het bytebereik in uw toepassingslogica uit.<br></br>Kies ervoor om gegevens voor deze toepassing in lokaal vastgemaakte volumes te plaatsen in tegenstelling tot gelaagde volumes.<br></br>*Waarschuwing:* Als het gebruik van lokaal vastgemaakte volumes en bytebereikvergrendeling is ingeschakeld, moet u er rekening mee houden dat het lokaal vastgemaakte volume online kan zijn nog voordat het herstel is voltooid. In dergelijke gevallen, als een herstel aan de gang is, moet u wachten tot het herstel is voltooid. |
| **7.** |Gelaagde aandelen |Werken met grote bestanden kan resulteren in een langzame laag uit. |Bij het werken met grote bestanden raden we aan dat het grootste bestand kleiner is dan 3% van de grootte van het aandeel. |
| **8.** |Gebruikte capaciteit voor aandelen |Mogelijk ziet u het verbruik van aandelen als er geen gegevens over het aandeel zijn. Dit komt omdat de gebruikte capaciteit voor aandelen metadata bevat. | |
| **9.** |Herstel na noodgeval |U het noodherstel van een bestandsserver alleen uitvoeren naar hetzelfde domein als dat van het bronapparaat. Noodherstel naar een doelapparaat in een ander domein wordt niet ondersteund in deze release. |Dit zal worden uitgevoerd in een latere release. |
| **10.** |Azure PowerShell |De virtuele StorSimple-apparaten kunnen in deze versie niet worden beheerd via de Azure PowerShell. |Al het beheer van de virtuele apparaten moet worden gedaan via de Azure classic portal en de lokale web-ui. |
| **11.** |Wachtwoord wijzigen |De console van het virtual array-apparaat accepteert alleen invoer in de en-AMERIKAANSE toetsenbordindeling. | |
| **12.** |CHAP |CHAP-referenties die eenmaal zijn gemaakt, kunnen niet worden verwijderd. Bovendien, als u de CHAP-referenties wijzigt, moet u de volumes offline halen en ze vervolgens online brengen voordat de wijziging van kracht wordt. |Deze zullen worden aangepakt in een latere release. |
| **13.** |iSCSI-server |De 'Gebruikte opslag' die wordt weergegeven voor een iSCSI-volume kan verschillen in de StorSimple Manager-service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteemweergave.<br></br>Het apparaat ziet de blokken toegewezen wanneer het volume was op de maximale grootte. |
| **14.** |Bestandsserver* |Als aan een bestand in een map een alternatieve gegevensstroom (ADS) is gekoppeld, wordt er geen back-up van het ADS gemaakt of hersteld via herstel, kloonen en Itemlevel Recovery. | |

## <a name="next-step"></a>Volgende stap
[Installeer updates](storsimple-ova-install-update-01.md) op uw StorSimple Virtual Array.

