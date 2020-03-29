---
title: StorSimple 8000 Series Update 2.2 release notes | Microsoft Documenten
description: Beschrijft de nieuwe functies, problemen en oplossingen voor StorSimple 8000 Series Update 2.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 12d11cddf077d4d07732490255d44e89ddaf3217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531051"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 Series Update 2.2 release notes StorSimple 8000 Series Update 2.2 release notes StorSimple 8000 Series Update 2.2 release notes Stor

## <a name="overview"></a>Overzicht
De volgende release notes beschrijven de nieuwe functies en identificeren de kritieke open problemen voor StorSimple 8000 Series Update 2.2. Ze bevatten ook een lijst van de StorSimple software-updates opgenomen in deze release.

Update 2.2 kan worden toegepast op elk StorSimple-apparaat met Release (GA) of Update 0.1 tot en met Update 2.1. De apparaatversie die is gekoppeld aan Update 2.2 is 6.3.9600.17708.

Bekijk de informatie in de releasenotes voordat u de update implementeert in uw StorSimple-oplossing.

> [!IMPORTANT]
> * Update 2.2 heeft alleen software-updates. Het duurt ongeveer 1,5-2 uur om deze update te installeren. 
> * Als u Update 2.1 uitvoert, raden we u aan Update 2.2 zo snel mogelijk toe te passen.
> * Voor nieuwe releases ziet u mogelijk niet meteen updates omdat we een gefaseerde implementatie van de updates uitvoeren. Wacht een paar dagen, en dan scannen voor updates weer als deze binnenkort beschikbaar zal komen.
> 
> 

## <a name="whats-new-in-update-22"></a>Nieuwe informatie in Update 2.2
De volgende belangrijke verbeteringen zijn aangebracht in Update 2.2.

* **Optimalisatie van geautomatiseerde ruimteterugwinning** – Wanneer gegevens worden verwijderd op dun ingerichte volumes, moeten de ongebruikte opslagblokken worden teruggewonnen. Deze release heeft het ruimteterugwinningsproces van de cloud verbeterd, waardoor de ongebruikte ruimte sneller beschikbaar komt in vergelijking met de vorige versies.
* **Verbeteringen in momentopnameprestaties** - Update 2.2 heeft de tijd verbeterd om een cloudmomentopname te verwerken in bepaalde scenario's waarin grote volumes worden gebruikt en er minimaal tot geen gegevensverloop is. Een scenario dat zou profiteren van deze verbetering zou de archiefvolumes.
* **Verharding van het verzamelen van ondersteuningspakketten** - Er zijn verbeteringen aangebracht in de manier waarop het ondersteuningspakket wordt verzameld en geüpload in deze release. 
* **Verbeteringen in de betrouwbaarheid van updates** - Deze release heeft bugfixes die resulteren in een verbeterde betrouwbaarheid van de update.

## <a name="issues-fixed-in-update-22"></a>Problemen opgelost in Update 2.2
In de volgende tabellen vindt u een overzicht van problemen die zijn opgelost in Updates 2.2 en 2.1.    

| Nee | Functie | Probleem | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Hostprestaties |In de eerdere release werden prestatieproblemen aan hostzijde waargenomen tijdens het maken van een lokaal vastgemaakt volume en tijdens de conversie van een gelaagd volume naar een lokaal vastgemaakt volume. Deze problemen worden opgelost in deze release, wat resulteert in een verbetering van de hostprestaties tijdens de volumecreatie- en conversieprocedures. |Ja |Nee |
| 2 |Lokaal vastgemaakte volumes |In zeldzame gevallen loopt het systeem vast bij het maken van een lokaal vastgemaakt volume. Deze bug is opgelost in deze release. |Ja |Nee |
| 3 |Tiering |Er waren sporadische crashes toen de metadata voor de StorSimple Cloud Appliances (8010 en 8020) naar de cloud werden geslopen. Dit probleem is opgelost in deze release. |Nee |Ja |
| 4 |Maken van momentopnamen |Er waren problemen met betrekking tot het maken van incrementele momentopnamen in scenario's met grote volumes en minimaal tot geen gegevensverloop. Deze problemen zijn opgelost in deze release. |Ja |Ja |
| 5 |Openstack-verificatie |Bij het gebruik van Openstack als de cloud service provider, zou de gebruiker lopen in een zeldzame bug in verband met de authenticatie waar de JSON parser resulteerde in een crash. Deze bug is opgelost in deze release. |Ja |Nee |
| 6 |Exemplaar aan hostzijde |In eerdere versies van software werd een zeldzame bug met betrekking tot de ODX-timing waargenomen bij het kopiëren van de gegevens van het ene volume naar het andere volume. Dit zou resulteren in een controller failover en het systeem zou kunnen gaan in recovery mode. Deze bug is opgelost in deze release. |Ja |Nee |
| 7 |Windows Management Instrumentation (WMI) |In de vorige versies van software waren er verschillende gevallen\<van falen van webproxy met de uitzondering "ManagementException> Provider load failure". Deze bug werd toegeschreven aan een WMI-geheugenlek en is nu opgelost. |Ja |Nee |
| 8 |Update |In bepaalde zeldzame gevallen, in de vorige versies van software, de gebruiker ontving een "CisPowershellHcsscripterror" bij het proberen om updates te scannen of te installeren. Dit probleem is opgelost in deze release. |Ja |Ja |
| 9 |Ondersteuningspakket |In deze release zijn er verbeteringen aangebracht in de manier waarop het Ondersteuningspakket wordt verzameld en geüpload. |Ja |Ja |

## <a name="known-issues-in-update-22"></a>Bekende problemen in Update 2.2
De volgende tabel bevat een overzicht van bekende problemen in deze release.

| Nee. | Functie | Probleem | Opmerkingen / tijdelijke oplossing | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Schijfquorum |In zeldzame gevallen, als de meeste schijven in de EBOD-behuizing van een 8600-apparaat zijn losgekoppeld, wat resulteert in geen schijfquorum, gaat de opslaggroep offline. Het blijft offline, zelfs als de schijven opnieuw zijn aangesloten. |U moet het apparaat opnieuw opstarten. Als het probleem blijft bestaan, neemt u contact op met Microsoft Support voor de volgende stappen. |Ja |Nee |
| 2 |Onjuiste controller-id |Wanneer een controllervervanging wordt uitgevoerd, kan controller 0 worden weergegeven als controller 1. Tijdens het vervangen van de controller, wanneer de afbeelding wordt geladen vanaf het peer-knooppunt, kan de controller-ID in eerste instantie worden weergegeven als de ID van de peer controller. In zeldzame gevallen kan dit gedrag ook worden gezien na een herstart van het systeem. |Er is geen actie van de gebruiker vereist. Deze situatie lost zichzelf op nadat de vervanging van de controller is voltooid. |Ja |Nee |
| 3 |Opslagaccounts |Het gebruik van de opslagservice om het opslagaccount te verwijderen is een niet-ondersteund scenario. Dit zal leiden tot een situatie waarin gebruikersgegevens niet kunnen worden opgehaald. | |Ja |Ja |
| 4 |Apparaatfailover |Meerdere failovers van een volumecontainer van hetzelfde bronapparaat naar verschillende doelapparaten worden niet ondersteund. Failover van een enkel dode apparaat naar meerdere apparaten zal het volume containers op de eerste mislukte over apparaat verliezen eigendom van gegevens. Na een dergelijke failover worden deze volumecontainers anders weergegeven of gedragen ze zich anders wanneer u ze bekijkt in de klassieke Azure-portal. | |Ja |Nee |
| 5 |Installeren |Tijdens de StorSimple-adapter voor SharePoint-installatie moet u een apparaat-IP verstrekken om de installatie succesvol te laten voltooien. | |Ja |Nee |
| 6 |Webproxy |Als uw webproxyconfiguratie HTTPS als opgegeven protocol heeft, wordt de communicatie tussen apparaat en service beïnvloed en wordt het apparaat offline gegaan. Ondersteuningspakketten worden ook gegenereerd in het proces, waarbij aanzienlijke bronnen op uw apparaat worden verbruikt. |Zorg ervoor dat de URL van de webproxy HTTP als opgegeven protocol heeft. Zie [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie. |Ja |Nee |
| 7 |Webproxy |Als u webproxy configureert en inschakelt op een geregistreerd apparaat, moet u de actieve controller op uw apparaat opnieuw starten. | |Ja |Nee |
| 8 |Hoge cloudlatentie en hoge I/O-workload |Wanneer uw StorSimple-apparaat een combinatie van zeer hoge cloudlatencies (volgorde van seconden) en hoge I/O-workload tegenkomt, gaan de apparaatvolumes in een gedegradeerde status en kunnen de I/O's mislukken met een fout 'apparaat dat nog niet klaar is'. |U moet de apparaatcontrollers handmatig opnieuw opstarten of een apparaatfailover uitvoeren om te herstellen van deze situatie. |Ja |Nee |
| 9 |Azure PowerShell |Wanneer u de StorSimple-cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wacht** met het selecteren van het eerste object, zodat u een nieuw **object VolumeContainer** maken, retourneert de cmdlet alle objecten. |Wikkel de cmdlet tussen haakjes als volgt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Ja |Ja |
| 10 |Migratie |Wanneer meerdere volumecontainers worden doorgegeven voor migratie, is de ETA voor de nieuwste back-up alleen nauwkeurig voor de eerste volumecontainer. Bovendien wordt parallelle migratie gestart nadat de eerste 4 back-ups in de eerste volumecontainer zijn gemigreerd. |We raden u aan één volumecontainer tegelijk te migreren. |Ja |Nee |
| 11 |Migratie |Na het herstellen worden volumes niet toegevoegd aan het back-upbeleid of de virtuele schijfgroep. |U moet deze volumes toevoegen aan een back-upbeleid om back-ups te maken. |Ja |Ja |
| 12 |Migratie |Nadat de migratie is voltooid, mag het apparaat uit de 5000/7000-serie geen toegang krijgen tot de gemigreerde gegevenscontainers. |We raden u aan de gemigreerde gegevenscontainers te verwijderen nadat de migratie is voltooid en vastgelegd. |Ja |Nee |
| 13 |Kloon en DR |Een StorSimple-apparaat met update 1 kan disaster recovery niet klonen of uitvoeren naar een apparaat waarop pre-update 1-software wordt uitgevoerd. |U moet het doelapparaat bijwerken naar Update 1 om deze bewerkingen mogelijk te maken |Ja |Ja |
| 14 |Migratie |Configuratieback-up voor migratie kan mislukken op een apparaat uit de 5000-7000-serie wanneer er volumegroepen zijn zonder bijbehorende volumes. |Verwijder alle lege volumegroepen zonder gekoppelde volumes en probeer vervolgens opnieuw de configuratieback-up. |Ja |Nee |
| 15 |Azure PowerShell-cmdlets en lokaal vastgemaakte volumes |U geen lokaal vastgemaakt volume maken via Azure PowerShell-cmdlets. (Elk volume dat u maakt via Azure PowerShell, wordt gelaagd.) |Gebruik altijd de StorSimple Manager-service om lokaal vastgemaakte volumes te configureren. |Ja |Nee |
| 16 |Beschikbare ruimte voor lokaal vastgemaakte volumes |Als u een lokaal vastgemaakt volume verwijdert, wordt de beschikbare ruimte voor nieuwe volumes mogelijk niet onmiddellijk bijgewerkt. De StorSimple Manager-service werkt de beschikbare lokale ruimte ongeveer elk uur bij. |Wacht een uur voordat u het nieuwe volume probeert te maken. |Ja |Nee |
| 17 |Lokaal vastgemaakte volumes |Met uw hersteltaak wordt de tijdelijke momentopnameback-up in de back-upcatalogus weergeven, maar alleen voor de duur van de hersteltaak. Bovendien wordt een virtuele schijfgroep met **voorvoegseltmpCollection** weergegeven op de pagina **Back-upbeleid,** maar alleen voor de duur van de hersteltaak. |Dit gedrag kan optreden als uw hersteltaak alleen lokaal vastgemaakte volumes of een mix van lokaal vastgemaakte en gelaagde volumes heeft. Als de hersteltaak alleen gelaagde volumes bevat, treedt dit gedrag niet op. Er is geen tussenkomst van de gebruiker is vereist. |Ja |Nee |
| 18 |Lokaal vastgemaakte volumes |Als u een hersteltaak annuleert en een mislukte controller onmiddellijk daarna optreedt, wordt de hersteltaak **mislukt** weergegeven in plaats van **Geannuleerd**. Als een hersteltaak mislukt en een mislukte controller onmiddellijk daarna optreedt, wordt op de hersteltaak **Geannuleerd weergegeven** in plaats van **mislukt**. |Dit gedrag kan optreden als uw hersteltaak alleen lokaal vastgemaakte volumes of een mix van lokaal vastgemaakte en gelaagde volumes heeft. Als de hersteltaak alleen gelaagde volumes bevat, treedt dit gedrag niet op. Er is geen tussenkomst van de gebruiker is vereist. |Ja |Nee |
| 19 |Lokaal vastgemaakte volumes |Als u een hersteltaak annuleert of als een herstel mislukt en er vervolgens een mislukte controller optreedt, wordt er een extra hersteltaak weergegeven op de pagina **Taken.** |Dit gedrag kan optreden als uw hersteltaak alleen lokaal vastgemaakte volumes of een mix van lokaal vastgemaakte en gelaagde volumes heeft. Als de hersteltaak alleen gelaagde volumes bevat, treedt dit gedrag niet op. Er is geen tussenkomst van de gebruiker is vereist. |Ja |Nee |
| 20 |Lokaal vastgemaakte volumes |Als u een gelaagd volume (gemaakt en gekloond met Update 1.2 of eerder) probeert om te zetten naar een lokaal vastgemaakt volume en uw apparaat geen ruimte meer heeft of als er een wolkstoring is, kan de kloon(s) worden beschadigd. |Dit probleem treedt alleen op bij volumes die zijn gemaakt en gekloond met pre-Update 2.1-software. Dit zou een zeldzaam scenario moeten zijn. | | |
| 21 |Volumeconversie |Werk de ACL's die aan een volume zijn gekoppeld niet bij terwijl een volumeconversie wordt uitgevoerd (gelaagd naar lokaal vastgemaakt of vice versa). Het bijwerken van de ADR's kan leiden tot beschadiging van gegevens. |Werk indien nodig de ADR's bij voorafgaand aan de volumeconversie en maak geen verdere ACR-updates terwijl de conversie aan de gang is. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Controller- en firmware-updates in Update 2.2
Deze release heeft software-only updates. Als u echter een versie voor update 2 bijwerkt, moet u stuurprogramma-, Storport-, Spaceport- en (in sommige gevallen) firmware-updates voor schijven op uw apparaat installeren.

Zie [Update 2.2 installeren](storsimple-install-update-21.md) op uw StorSimple-apparaat voor meer informatie over het installeren van de firmware-updates van het stuurprogramma, Storport, Spaceport en schijf.

## <a name="virtual-device-updates-in-update-22"></a>Updates van virtuele apparaten in Update 2.2
Deze update kan niet worden toegepast op het virtuele apparaat. Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap
Meer informatie over het [installeren van Update 2.2](storsimple-install-update-21.md) op uw StorSimple-apparaat.

