---
title: StorSimple 8000 Series Update 3 release notes
description: Beschrijft de nieuwe functies, problemen en oplossingen voor StorSimple 8000 Series Update 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254610"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Update 3 release notes voor uw StorSimple 8000 serie apparaat

## <a name="overview"></a>Overzicht
De volgende release notes beschrijven de nieuwe functies en identificeren de kritieke open problemen voor StorSimple 8000 Series Update 3. Ze bevatten ook een lijst van de StorSimple software-updates opgenomen in deze release. 

Update 3 kan worden toegepast op elk StorSimple-apparaat met Release (GA) of Update 0.1 tot en met Update 2.2. De apparaatversie die is gekoppeld aan Update 3 is 6.3.9600.17759.

Bekijk de informatie in de releasenotes voordat u de update implementeert in uw StorSimple-oplossing.

> [!IMPORTANT]
> * Update 3 heeft apparaatsoftware, LSI-stuurprogramma en firmware en Storport- en Spaceport-updates. Het duurt ongeveer 1,5-2 uur om deze update te installeren. 
> * Voor nieuwe releases ziet u mogelijk niet meteen updates omdat we een gefaseerde implementatie van de updates uitvoeren. Wacht een paar dagen, en dan scannen voor updates weer als deze binnenkort beschikbaar zal komen.
> 
> 

## <a name="whats-new-in-update-3"></a>Nieuwe informatie in Update 3
De volgende belangrijke verbeteringen en bug fixes zijn gemaakt in Update 3.

* **Automatische ruimteterugwinning verandert** - Vanaf update 3, de ruimte terugwinning algoritmen draaien op de stand-by controller van het systeem resulteert in een snellere uitvoering. Voor meer informatie over de poorten die nodig zijn om te werken met ruimteterugwinning, verwijzen wij u naar de [StorSimple netwerkvereisten.](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)
* **Prestatieverbeteringen** – Update 3 heeft de leesschrijfprestaties in de cloud verbeterd.
* **Migratiegerelateerde verbeteringen** - In deze release zijn verschillende bugfixes en verbeteringen uitgevoerd voor de migratiefunctie van apparaten uit de 5000/7000-serie naar apparaten uit de 8000-serie. Ga voor meer informatie over het gebruik van de migratiefunctie naar [Migratie van het apparaat uit de 5000/7000-serie naar het apparaat uit de 8000-serie.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) 
* **Monitoring gerelateerde fixes** - In deze release werden bugs met betrekking tot het bewaken van grafieken, servicedashboard en apparaatdashboard opgelost.

## <a name="issues-fixed-in-update-3"></a>Problemen opgelost in update 3
In de volgende tabellen vindt u een overzicht van problemen die zijn opgelost in update 3.    

| Nee | Functie | Probleem | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Gegevensmigratie aan hostzijde |In de eerdere release ging het StorSimple Cloud Appliance offline tijdens een datamigratie aan de hostzijde. Dit probleem is opgelost in deze release. |Nee |Ja |
| 2 |Lokaal vastgemaakte volumes |In de vorige versie waren er problemen met I/O-fouten, volumeconversiefouten en gegevenspadfouten voor lokaal vastgemaakte volumes. Deze problemen werden veroorzaakt door de wortel en opgelost in deze release. |Ja |Nee |
| 3 |Bewaking |Er waren meerdere problemen met betrekking tot rapportage-eenheden en bewaking, evenals dashboarddiagrammen voor apparaten waarin onjuiste informatie werd weergegeven voor lokaal vastgemaakte volumes. Deze problemen zijn opgelost in deze release. |Ja |Nee |
| 4 |Zware schrijft I/O |Bij het gebruik van StorSimple voor workloads met zware schrijfbewerkingen, zou de gebruiker lopen in een zeldzame bug waar de werkset werd gelaagd in de cloud. Deze bug is opgelost in deze release. |Ja |Ja |
| 5 |Back-up |In bepaalde zeldzame gevallen, in de vorige versies van software, wanneer de gebruiker een back-up van een externe kloon nam, zouden ze in de cloud fouten lopen en de bewerking zou fout uit. In deze release is het probleem opgelost en is de bewerking voltooid. |Ja |Ja |
| 6 |Back-upbeleid |In bepaalde zeldzame gevallen, in de eerdere versies van software, was er een bug in verband met het verwijderen van back-up beleid. Dit probleem is opgelost in deze release. |Ja |Ja |

## <a name="known-issues-in-update-3"></a>Bekende problemen in update 3
De volgende tabel bevat een overzicht van bekende problemen in deze release.

| Nee. | Functie | Probleem | Opmerkingen / tijdelijke oplossing | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Schijfquorum |In zeldzame gevallen, als de meeste schijven in de EBOD-behuizing van een 8600-apparaat zijn losgekoppeld, wat resulteert in geen schijfquorum, gaat de opslaggroep offline. Het blijft offline, zelfs als de schijven opnieuw zijn aangesloten. |U moet het apparaat opnieuw opstarten. Als het probleem blijft bestaan, neemt u contact op met Microsoft Support voor de volgende stappen. |Ja |Nee |
| 2 |Onjuiste controller-id |Wanneer een controllervervanging wordt uitgevoerd, kan controller 0 worden weergegeven als controller 1. Tijdens het vervangen van de controller, wanneer de afbeelding wordt geladen vanaf het peer-knooppunt, kan de controller-ID in eerste instantie worden weergegeven als de ID van de peer controller. In zeldzame gevallen kan dit gedrag ook worden gezien na een herstart van het systeem. |Er is geen actie van de gebruiker vereist. Deze situatie lost zichzelf op nadat de vervanging van de controller is voltooid. |Ja |Nee |
| 3 |Opslagaccounts |Het gebruik van de opslagservice om het opslagaccount te verwijderen is een niet-ondersteund scenario. Dit zal leiden tot een situatie waarin gebruikersgegevens niet kunnen worden opgehaald. | |Ja |Ja |
| 4 |Apparaatfailover |Meerdere failovers van een volumecontainer van hetzelfde bronapparaat naar verschillende doelapparaten worden niet ondersteund. Failover van een enkel dode apparaat naar meerdere apparaten zal het volume containers op de eerste mislukte over apparaat verliezen eigendom van gegevens. Na een dergelijke failover worden deze volumecontainers anders weergegeven of gedragen ze zich anders wanneer u ze bekijkt in de klassieke Azure-portal. | |Ja |Nee |
| 5 |Installeren |Tijdens de StorSimple-adapter voor SharePoint-installatie moet u een apparaat-IP verstrekken om de installatie succesvol te laten voltooien. | |Ja |Nee |
| 6 |Webproxy |Als uw webproxyconfiguratie HTTPS als opgegeven protocol heeft, wordt de communicatie tussen apparaat en service beïnvloed en wordt het apparaat offline gegaan. Ondersteuningspakketten worden ook gegenereerd in het proces, waarbij aanzienlijke bronnen op uw apparaat worden verbruikt. |Zorg ervoor dat de URL van de webproxy HTTP als opgegeven protocol heeft. Zie [Configure web proxy for your device](storsimple-8000-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie. |Ja |Nee |
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
| 22 |Updates |Wanneer u Update 3 toepast, wordt op de **pagina Onderhoud** in de klassieke Azure-portal het volgende bericht weergegeven met betrekking tot Update 2 - "StorSimple 8000-serie Update 2 bevat de mogelijkheid voor Microsoft om proactief logboekgegevens van uw apparaat te verzamelen wanneer we potentiële problemen detecteren". Dit is misleidend omdat het aangeeft dat het apparaat wordt bijgewerkt naar Update 2. Nadat het apparaat is bijgewerkt naar Update 3, verdwijnt dit bericht. |Dit gedrag wordt opgelost in een toekomstige release. |Ja |Nee |

## <a name="controller-and-firmware-updates-in-update-3"></a>Controller- en firmware-updates in update 3
Deze release heeft LSI driver en firmware updates. Zie [Update 3 installeren](storsimple-install-update-3.md) op uw StorSimple-apparaat voor meer informatie over het installeren van het LSI-stuurprogramma en firmware-updates.

## <a name="virtual-device-updates-in-update-3"></a>Updates van virtuele apparaten in update 3
Deze update kan niet worden toegepast op het StorSimple Cloud Appliance (ook wel het virtuele apparaat genoemd). Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap
Meer informatie over het [installeren van Update 3](storsimple-install-update-3.md) op uw StorSimple-apparaat.

