---
title: StorSimple 8000 Series Update 1.2 release notes | Microsoft Documenten
description: Beschrijft de nieuwe functies, problemen en oplossingen voor StorSimple 8000 Series Update 1.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11138857e33eec0f854ddb61956ea24c858c49a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60530994"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Update 1.2 release notes voor uw StorSimple 8000 serie apparaat

## <a name="overview"></a>Overzicht
De volgende release notes beschrijven de nieuwe functies en identificeren de kritieke open problemen voor StorSimple 8000 Series Update 1.2. Ze bevatten ook een lijst van de StorSimple software, driver en disk firmware updates opgenomen in deze release. 

Update 1.2 kan worden toegepast op elk StorSimple-apparaat met Release (GA), Update 0.1, Update 0.2 of Update 0.3-software. Update 1.2 is niet beschikbaar als op uw apparaat update 1 of update 1.1 wordt uitgevoerd. Als op uw apparaat Release (GA) wordt uitgevoerd, neemt u [contact op met Microsoft Support](storsimple-contact-microsoft-support.md) om u te helpen bij het installeren van deze update.

In de volgende tabel worden de versies van de apparaatsoftware weergegeven die overeenkomen met updates 1, 1.1 en 1.2.

| Als het uitvoeren van update ... | dit is uw apparaat software versie. |
| --- | --- |
| Update 1.2 |6.3.9600.17584 |
| Update 1.1 |6.3.9600.17521 |
| Update 1.0 |6.3.9600.17491 |

Bekijk de informatie in de releasenotes voordat u de update implementeert in uw StorSimple-oplossing. Zie voor meer informatie hoe [u Update 1.2 op uw StorSimple-apparaat installeert.](storsimple-install-update-1.md) 

> [!IMPORTANT]
> * Het duurt ongeveer 5-10 uur om deze update te installeren (inclusief de Windows-updates). 
> * Update 1.2 heeft software, LSI driver en disk firmware updates. Volg de instructies in [installatie Update 1.2 op uw StorSimple-apparaat](storsimple-install-update-1.md)om te installeren.
> * Voor nieuwe releases ziet u mogelijk niet meteen updates omdat we een gefaseerde implementatie van de updates uitvoeren. Scan naar updates in een paar dagen weer als deze binnenkort beschikbaar zal komen.
> 
> 

## <a name="whats-new-in-update-12"></a>Nieuwe informatie in Update 1.2
Deze functies werden voor het eerst uitgebracht met Update 1 die beschikbaar werd gesteld aan een beperkte set gebruikers. Met de Update 1.2 release, de meeste van de StorSimple gebruikers zou zien de volgende nieuwe functies en verbeteringen:

* **Migratie van 5000-7000-serie apparaten naar 8000-serie** - Deze release introduceert een nieuwe migratiefunctie waarmee de toestelgebruikers uit de StorSimple 5000-7000-serie hun gegevens kunnen migreren naar een fysiek toestel uit de StorSimple 8000-serie of een virtueel toestel. De migratiefunctie heeft twee belangrijke waardeproposities:                                                                  
  
  * **Bedrijfscontinuïteit**, door migratie van bestaande gegevens over 5000-7000-serie toestellen naar 8000-serie toestellen mogelijk te maken.
  * **Verbeterde functie aanbod van de 8000-serie apparaten,** zoals efficiënt gecentraliseerd beheer van meerdere apparaten via StorSimple Manager service, betere klasse van hardware en bijgewerkte firmware, virtuele apparaten, data mobiliteit, en functies in de toekomst roadmap.
    
    Raadpleeg de [migratiegids](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) voor meer informatie over het migreren van een StorSimple 5000-7000-serie naar een apparaat uit de 8000-serie. 
* **Beschikbaarheid in de Azure Government Portal** – StorSimple is nu beschikbaar in de Azure Government-portal. Bekijk hoe [u een StorSimple-apparaat implementeert in de Azure Government Portal.](storsimple-deployment-walkthrough-gov.md)
* **Ondersteuning voor andere cloudserviceproviders** - De andere ondersteunde cloudserviceproviders zijn Amazon S3, Amazon S3 met RRS, HP en OpenStack (bèta).
* **Update naar de nieuwste Storage API's** - Met deze release is StorSimple bijgewerkt naar de nieuwste Azure Storage-service API's. StorSimple 8000-serie apparaten die pre-Update 1-softwareversies uitvoeren (Release, 0.1, 0.2 en 0.3) gebruiken versies van de Azure Storage Service API's die ouder zijn dan 17 juli 2009. Zoals vermeld in de bijgewerkte [aankondiging over het verwijderen van storage service versies](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), door augustus 1, 2016, deze API's zullen worden afgeschaft. Het is absoluut noodzakelijk dat u de StorSimple 8000 Series Update 1 toepast vóór 1 augustus 2016. Als u dit niet doet, zullen StorSimple-apparaten niet meer goed functioneren.
* **Ondersteuning voor Zone Redundant Storage (ZRS)** – Met de upgrade naar de nieuwste versie van de Storage API's ondersteunt de StorSimple 8000-serie Zone Redundant Storage (ZRS) naast Locally Redundant Storage (LRS) en Geo-redundante Storage (GRS). Raadpleeg dit [artikel over redundantieopties](../storage/common/storage-redundancy.md) voor Azure Storage voor ZRS-details.
* **Verbeterde initiële implementatie- en update-ervaring** - In deze release zijn de installatie- en updateprocessen verbeterd. De installatie via de installatiewizard is verbeterd om feedback te geven aan de gebruiker als de netwerkconfiguratie- en firewall-instellingen onjuist zijn. Er zijn extra diagnostische cmdlets beschikbaar om u te helpen bij het oplossen van problemen met netwerken van het apparaat. Zie het [artikel over de implementatie van probleemoplossing](storsimple-troubleshoot-deployment.md) voor meer informatie over de nieuwe diagnostische cmdlets die worden gebruikt voor het oplossen van problemen.

## <a name="issues-fixed-in-update-12"></a>Problemen opgelost in Update 1.2
In de volgende tabel vindt u een overzicht van problemen die zijn opgelost in updates 1.2, 1.1 en 1.    

| Nee. | Functie | Probleem | Opgelost in Update | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell voor StorSimple |Toen een gebruiker op afstand toegang had tot het StorSimple-apparaat met Windows PowerShell voor StorSimple en vervolgens de installatiewizard startte, trad een crash op zodra het IP-adres van Gegevens 0 werd ingevoerd. Deze bug is nu opgelost in Update 1. |Update 1 |Ja |Ja |
| 2 |Fabrieksinstellingen terugzetten |In sommige gevallen, wanneer u een fabrieksreset uitvoerde, kwam het StorSimple-apparaat vast te zitten en werd dit bericht weergegeven: **Resetten naar de fabriek is in uitvoering (fase 8).** Dit gebeurde als u ctrl+c hebt ingedrukt terwijl de cmdlet aan de gang was. Deze bug is nu opgelost. |Update 1 |Ja |Nee |
| 3 |Fabrieksinstellingen terugzetten |Na een mislukte fabrieksreset met dubbele controller mocht u doorgaan met de registratie van het apparaat. Dit resulteerde in een niet-ondersteunde systeemconfiguratie. In update 1 wordt een foutbericht weergegeven en wordt de registratie geblokkeerd op een apparaat met een mislukte fabrieksreset. |Update 1 |Ja |Nee |
| 4 |Fabrieksinstellingen terugzetten |In sommige gevallen werden fout-positieve mismatchwaarschuwingen verhoogd. Onjuiste mismatchwaarschuwingen worden niet meer gegenereerd op apparaten met update 1. |Update 1 |Ja |Nee |
| 5 |Fabrieksinstellingen terugzetten |Als een fabrieksreset vóór de voltooiing werd onderbroken, is het apparaat de herstelmodus ingevoerd en hebt u geen toegang tot Windows PowerShell voor StorSimple. Deze bug is nu opgelost. |Update 1 |Ja |Nee |
| 6 |Herstel na noodgeval |Een disaster recovery (DR) bug werd opgelost waarbij DR zou mislukken tijdens de ontdekking van back-ups op het doelapparaat. |Update 1 |Ja |Ja |
| 7 |LED's controleren |In bepaalde gevallen gaf het toezicht op LED's aan de achterkant van het toestel niet de juiste status aan. De blauwe LED werd uitgeschakeld. DATA 0 en DATA 1 LED's knipperden zelfs wanneer deze interfaces niet waren geconfigureerd. Het probleem is opgelost en het monitoren van LED's geeft nu de juiste status aan. |Update 1 |Ja |Nee |
| 8 |LED's controleren |In bepaalde gevallen is na het toepassen van Update 1 het blauwe lampje op de actieve controller uitgeschakeld, waardoor het moeilijk is om de actieve controller te identificeren. Dit probleem is opgelost in deze patch release. |Update 1.2 |Ja |Nee |
| 9 |Netwerkinterfaces |In eerdere versies kan een StorSimple-apparaat dat is geconfigureerd met een niet-routable gateway offline gaan. In deze versie is de routeringsstatistiek voor Gegevens 0 het laagst gemaakt; Daarom, zelfs als andere netwerkinterfaces cloud-enabled zijn, wordt al het cloudverkeer van het apparaat via Gegevens 0 doorgestuurd. |Update 1 |Ja |Ja |
| 10 |Back-ups |Een bug in Update 1 waardoor back-ups na 24 dagen zijn mislukt, is opgelost in de patchrelease Update 1.1. |Update 1.1 |Ja |Ja |
| 11 |Back-ups |Een bug in eerdere versies resulteerde in slechte prestaties voor cloudsnapshots met lage wijzigingssnelheden. Deze bug is opgelost in deze patch release. |Update 1.2 |Ja |Ja |
| 12 |Updates |Een bug in Update 1 die een mislukte upgrade meldde en ervoor zorgde dat de controllers in de herstelmodus terecht kwamen, is opgelost in deze patchrelease. |Update 1.2 |Ja |Ja |

## <a name="known-issues-in-update-12"></a>Bekende problemen in Update 1.2
De volgende tabel bevat een overzicht van bekende problemen in deze release.

| Nee. | Functie | Probleem | Opmerkingen/tijdelijke oplossing | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- | --- |
| 1 |Schijfquorum |In zeldzame gevallen, als de meeste schijven in de EBOD-behuizing van een 8600-apparaat zijn losgekoppeld, wat resulteert in geen schijfquorum, is de opslaggroep offline. Het blijft offline, zelfs als de schijven opnieuw zijn aangesloten. |U moet het apparaat opnieuw opstarten. Als het probleem blijft bestaan, neemt u contact op met Microsoft Support voor de volgende stappen. |Ja |Nee |
| 2 |Onjuiste controller-id |Wanneer een controllervervanging wordt uitgevoerd, kan controller 0 worden weergegeven als controller 1. Tijdens het vervangen van de controller, wanneer de afbeelding wordt geladen vanaf het peer-knooppunt, kan de controller-ID in eerste instantie worden weergegeven als de ID van de peer controller. In zeldzame gevallen kan dit gedrag ook worden gezien na een herstart van het systeem. |Er is geen actie van de gebruiker vereist. Deze situatie lost zichzelf op nadat de vervanging van de controller is voltooid. |Ja |Nee |
| 3 |Opslagaccounts |Het gebruik van de opslagservice om het opslagaccount te verwijderen is een niet-ondersteund scenario. Dit zal leiden tot een situatie waarin gebruikersgegevens niet kunnen worden opgehaald. |Ja |Ja | |
| 4 |Apparaatfailover |Meerdere failovers van een volumecontainer van hetzelfde bronapparaat naar verschillende doelapparaten worden niet ondersteund. Apparaat failover van een enkel dode apparaat naar meerdere apparaten zal het volume containers op de eerste mislukte over apparaat verliezen eigendom van gegevens. Na een dergelijke failover worden deze volumecontainers anders weergegeven of gedragen ze zich anders wanneer u ze bekijkt in de klassieke Azure-portal. | |Ja |Nee |
| 5 |Installeren |Tijdens de StorSimple-adapter voor SharePoint-installatie moet u een apparaat-IP verstrekken om de installatie succesvol te laten voltooien. | |Ja |Nee |
| 6 |Webproxy |Als uw webproxyconfiguratie HTTPS als opgegeven protocol heeft, wordt de communicatie tussen apparaat en service beïnvloed en wordt het apparaat offline gegaan. Ondersteuningspakketten worden ook gegenereerd in het proces, waarbij aanzienlijke bronnen op uw apparaat worden verbruikt. |Zorg ervoor dat de URL van de webproxy HTTP als opgegeven protocol heeft. Zie [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie. |Ja |Nee |
| 7 |Webproxy |Als u webproxy configureert en inschakelt op een geregistreerd apparaat, moet u de actieve controller op uw apparaat opnieuw starten. | |Ja |Nee |
| 8 |Hoge cloudlatentie en hoge I/O-workload |Wanneer uw StorSimple-apparaat een combinatie van zeer hoge cloudlatencies (volgorde van seconden) en hoge I/O-workload tegenkomt, gaan de apparaatvolumes in een gedegradeerde status en kunnen de I/O's mislukken met een fout 'apparaat dat nog niet klaar is'. |U moet de apparaatcontrollers handmatig opnieuw opstarten of een apparaatfailover uitvoeren om te herstellen van deze situatie. |Ja |Nee |
| 9 |Azure PowerShell |Wanneer u de StorSimple-cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wacht** met het selecteren van het eerste object, zodat u een nieuw **object VolumeContainer** maken, retourneert de cmdlet alle objecten. |Wikkel de cmdlet tussen haakjes als volgt: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Ja |Ja |
| 10 |Migratie |Wanneer meerdere volumecontainers worden doorgegeven voor migratie, is de ETA voor de nieuwste back-up alleen nauwkeurig voor de eerste volumecontainer. Bovendien wordt parallelle migratie gestart nadat de eerste 4 back-ups in de eerste volumecontainer zijn gemigreerd. |We raden u aan één volumecontainer tegelijk te migreren. |Ja |Nee |
| 11 |Migratie |Na het herstellen worden volumes niet toegevoegd aan het back-upbeleid of de virtuele schijfgroep. |U moet deze volumes toevoegen aan een back-upbeleid om back-ups te maken. |Ja |Ja |
| 12 |Migratie |Nadat de migratie is voltooid, mag het apparaat uit de 5000/7000-serie geen toegang krijgen tot de gemigreerde gegevenscontainers. |We raden u aan de gemigreerde gegevenscontainers te verwijderen nadat de migratie is voltooid en vastgelegd. |Ja |Nee |
| 13 |Kloon en DR |Een StorSimple-apparaat met update 1 kan Disaster Recovery niet klonen of uitvoeren naar een apparaat waarop pre-update 1-software wordt uitgevoerd. |U moet het doelapparaat bijwerken naar Update 1 om deze bewerkingen mogelijk te maken |Ja |Ja |
| 14 |Migratie |Configuratieback-up voor migratie kan mislukken op een apparaat uit de 5000-7000-serie wanneer er volumegroepen zijn zonder bijbehorende volumes. |Verwijder alle lege volumegroepen zonder gekoppelde volumes en probeer vervolgens opnieuw de configuratieback-up. |Ja |Nee |

## <a name="physical-device-updates-in-update-12"></a>Fysieke apparaatupdates in Update 1.2
Als patchupdate 1.2 wordt toegepast op een fysiek apparaat (versies uitvoeren voorafgaand aan update 1), wordt de softwareversie gewijzigd in 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Controller- en firmware-updates in Update 1.2
Met deze release worden het stuurprogramma en de schijffirmware op uw apparaat bijgewerkt.

* Zie [Update 1 voor LSI SAS-controllers in Microsoft Azure StorSimple Appliance voor](https://support.microsoft.com/kb/3043005)meer informatie over de update van de SAS-controller. 
* Zie [Schijffirmware-update 1 voor Microsoft Azure StorSimple Appliance voor](https://support.microsoft.com/kb/3063416)meer informatie over de firmware-update van de schijf.

## <a name="virtual-device-updates-in-update-12"></a>Updates van virtuele apparaten in Update 1.2
Deze update kan niet worden toegepast op het virtuele apparaat. Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-steps"></a>Volgende stappen
* [Installeer Update 1.2 op uw apparaat](storsimple-install-update-1.md).

