---
title: StorSimple 8000 Series Update 2 release notes | Microsoft Documenten
description: Beschrijft de nieuwe functies, problemen en oplossingen voor StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: 4e57fffd2f74ae1b14f51537c92299607f193ad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934065"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 Series Update 2 release notes

## <a name="overview"></a>Overzicht
De volgende release notes beschrijven de nieuwe functies en identificeren de kritieke open problemen voor StorSimple 8000 Series Update 2. Ze bevatten ook een lijst van de StorSimple software, driver, en schijf firmware-updates opgenomen in deze release. 

Update 2 kan worden toegepast op elk StorSimple-apparaat met Release (GA) of Update 0.1 tot en met Update 1.2. De apparaatversie die is gekoppeld aan Update 2 is 6.3.9600.17673.

Bekijk de informatie in de releasenotes voordat u de update implementeert in uw StorSimple-oplossing.

> [!IMPORTANT]
> * Het duurt ongeveer 4-7 uur om deze update te installeren (inclusief de Windows-updates). 
> * Update 2 heeft software, LSI driver, en SSD firmware-updates.
> * Voor nieuwe releases ziet u mogelijk niet meteen updates omdat we een gefaseerde implementatie van de updates uitvoeren. Wacht een paar dagen, en dan scannen voor updates weer als deze binnenkort beschikbaar zal komen.
> 
> 

## <a name="whats-new-in-update-2"></a>Nieuwe informatie in Update 2
Update 2 introduceert de volgende nieuwe functies.

* **Lokaal vastgemaakte volumes** – In eerdere versies van de StorSimple 8000-serie werden blokken gegevens naar de cloud gegelaagdeerd op basis van het gebruik. Er was geen manier om te garanderen dat blokken zou op lokale verblijven. Wanneer u een volume maakt, u in update 2 een volume aanwijzen als lokaal vastgemaakt en worden primaire gegevens van dat volume niet naar de cloud gegelaagdeerd. Momentopnamen van lokaal vastgemaakte volumes worden nog steeds naar de cloud gekopieerd voor back-up, zodat de cloud kan worden gebruikt voor gegevensmobiliteit en disaster recovery-doeleinden. Bovendien u het volumetype wijzigen (dat wil zeggen gelaagde volumes converteren naar lokaal vastgemaakte volumes en lokaal vastgemaakte volumes converteren naar gelaagde). 
* **StorSimple virtuele apparaat verbeteringen** - Voorheen, de StorSimple 8000 serie gepositioneerd het virtuele apparaat als een ramp herstel of ontwikkeling / test oplossing. Er was slechts één model van virtueel apparaat (model 1100). Update 2 introduceert twee virtuele apparaatmodellen: 
  
  * 8010 (voorheen de 1100 genoemd) – Geen verandering; heeft een capaciteit van 30 TB en maakt gebruik van Azure-standaardopslag.
  * 8020 – Heeft een capaciteit van 64 TB en maakt gebruik van Azure Premium-opslag voor betere prestaties.
    
    Er is een enkele VHD voor beide virtuele apparaatmodellen (8010/8020). Wanneer u het virtuele apparaat voor het eerst start, detecteert het de platformparameters en past het de juiste modelversie toe.
* **Netwerkverbeteringen** – Update 2 bevat de volgende netwerkverbeteringen:
  
  * Meerdere NIC's kunnen worden ingeschakeld voor de cloud, zodat failover kan optreden als een NIC mislukt.
  * Verbeteringen in de routering, met vaste statistieken voor cloudblokken.
  * Online opnieuw proberen van mislukte bronnen voor een failover.
  * Nieuwe waarschuwingen voor servicefouten.
* **Verbeteringen bijwerken** – In update 1.2 en eerder is de StorSimple 8000-serie bijgewerkt via twee kanalen: Windows Update voor clustering, iSCSI, enzovoort, en Microsoft Update voor binaire bestanden en firmware.
    Update 2 maakt gebruik van Microsoft Update voor alle updatepakketten. Dit moet leiden tot minder tijd patchen of het doen van failovers. 
* **Firmware-updates** – De volgende firmware-updates zijn inbegrepen:
  
  * LSI: lsi_sas2.sys Product Versie 2.00.72.10
  * Alleen SSD (geen HDD-updates): XMGG, XGEG, KZ50, F6C2 en VR08
* **Proactieve ondersteuning** : update 2 stelt Microsoft in staat om aanvullende diagnostische informatie van het apparaat te halen. Wanneer ons operationele team apparaten identificeert die problemen hebben, zijn we beter uitgerust om informatie van het apparaat te verzamelen en problemen te diagnosticeren. **Door Update 2 te accepteren, stelt u ons in staat om deze proactieve ondersteuning te bieden.**    

## <a name="issues-fixed-in-update-2"></a>Problemen opgelost in Update 2
In de volgende tabellen vindt u een overzicht van problemen die zijn opgelost in Updates 2.    

| Nee. | Functie | Probleem | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Netwerkinterfaces |Na een upgrade naar Update 1 meldde de StorSimple Manager-service dat de Data2- en Data3-poorten op één controller zijn mislukt. Dit probleem is opgelost. |Ja |Nee |
| 2 |Updates |Na een upgrade naar Update 1 zijn er op meerdere apparaten meldingsmeldingen opgetreden in de klassieke Azure-portal. Dit probleem is opgelost. |Ja |Nee |
| 3 |Openstack-verificatie |Wanneer u Openstack als uw cloudserviceprovider gebruikt, u een foutmelding ontvangen dat uw cloudverificatietekenreeks te lang was. Dit probleem is opgelost. |Ja |Nee |

## <a name="known-issues-in-update-2"></a>Bekende problemen in Update 2
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
| 20 |Lokaal vastgemaakte volumes |Als u een gelaagd volume (gemaakt en gekloond met Update 1.2 of eerder) probeert om te zetten naar een lokaal vastgemaakt volume en uw apparaat geen ruimte meer heeft of als er een wolkstoring is, kan de kloon(s) worden beschadigd. |Dit probleem treedt alleen op bij volumes die zijn gemaakt en gekloond met pre-Update 2-software. Dit zou een zeldzaam scenario moeten zijn. | | |
| 21 |Volumeconversie |Werk de ACL's die aan een volume zijn gekoppeld niet bij terwijl een volumeconversie wordt uitgevoerd (gelaagd naar lokaal vastgemaakt of vice versa). Het bijwerken van de ADR's kan leiden tot beschadiging van gegevens. |Werk indien nodig de ADR's bij voorafgaand aan de volumeconversie en maak geen verdere ACR-updates terwijl de conversie aan de gang is. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Controller- en firmware-updates in update 2
Met deze release worden het stuurprogramma en de schijffirmware op uw apparaat bijgewerkt.

* Zie Microsoft Knowledge base artikel 3121900 voor meer informatie over de LSI-firmware-update. 
* Zie Microsoft Knowledge base-artikel 3121899 voor meer informatie over de firmware-update van de schijf.

## <a name="virtual-device-updates-in-update-2"></a>Updates van virtuele apparaten in update 2
Deze update kan niet worden toegepast op het virtuele apparaat. Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap
Meer informatie over het [installeren van Update 2](storsimple-install-update-2.md) op uw StorSimple-apparaat.

