---
title: Failover en disaster recovery voor StorSimple 8000 serie apparaat
description: Meer informatie over hoe u falen op uw StorSimple-apparaat voor zichzelf, een ander fysiek apparaat of een cloudtoestel.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 179bc5cdf982792f41e0dec209341f346959a31a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397513"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Failover en herstel na noodgevallen voor apparaat uit de StorSimple 8000-serie

## <a name="overview"></a>Overzicht

In dit artikel wordt de functie failover van het apparaat beschreven voor de Apparaten uit de StorSimple 8000-serie en hoe deze functie kan worden gebruikt om StorSimple-apparaten te herstellen als zich een ramp voordoet. StorSimple gebruikt apparaatfailover om de gegevens van een bronapparaat in het datacenter te migreren naar een ander doelapparaat. De richtlijnen in dit artikel zijn van toepassing op fysieke apparaten uit de StorSimple 8000-serie en cloudapparaten met softwareversies Update 3 en hoger.

StorSimple gebruikt het **apparaatblad** om de failoverfunctie van het apparaat te starten in het geval van een ramp. Dit blad bevat alle StorSimple-apparaten die zijn aangesloten op uw StorSimple Device Manager-service.

![Het blad van apparaten](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Disaster recovery (DR) en device failover

In een DR-scenario (disaster recovery) werkt het primaire apparaat niet meer. StorSimple gebruikt het primaire apparaat als _bron_ en verplaatst de bijbehorende cloudgegevens naar een ander _doelapparaat._ Dit proces wordt aangeduid als de *failover*. De volgende afbeelding illustreert het proces van failover.

![Wat gebeurt er in de failover van het apparaat?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Het doelapparaat voor een failover kan een fysiek apparaat of zelfs een cloudtoestel zijn. Het doelapparaat kan zich op dezelfde of een andere geografische locatie bevinden dan het bronapparaat.

Tijdens de failover u volumecontainers selecteren voor migratie. StorSimple wijzigt vervolgens het eigendom van deze volumecontainers van het bronapparaat naar het doelapparaat. Zodra de volumecontainers van eigenaar veranderen, verwijdert StorSimple deze containers van het bronapparaat. Nadat de verwijdering is voltooid, u het doelapparaat weer terugsturen. _Failback_ brengt het eigendom terug naar het oorspronkelijke bronapparaat.

### <a name="cloud-snapshot-used-during-device-failover"></a>Cloudmomentopname die wordt gebruikt tijdens de failover van het apparaat

Na een DR wordt de meest recente cloudback-up gebruikt om de gegevens naar het doelapparaat te herstellen. Zie [De StorSimple Device Manager-service gebruiken om een handmatige back-up te maken voor](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup)meer informatie over cloudsnapshots.

In een StorSimple 8000-serie wordt back-upbeleid gekoppeld aan back-ups. Als er meerdere back-upbeleidsregels zijn voor hetzelfde volume, selecteert StorSimple het back-upbeleid met het grootste aantal volumes. StorSimple gebruikt vervolgens de meest recente back-up van het geselecteerde back-upbeleid om de gegevens op het doelapparaat te herstellen.

Stel dat er twee back-upbeleidsregels zijn, *defaultPol* en *customPol:*

* *defaultPol*: Een volume, *vol1,* draait dagelijks vanaf 22:30 uur.
* *customPol*: Vier volumes, *vol1*, *vol2*, *vol3*, *vol4*, draait dagelijks vanaf 22:00 uur.

In dit geval geeft StorSimple prioriteit aan crashconsistentie en gebruikt *het customPol* omdat het meer volumes heeft. De meest recente back-up van dit beleid wordt gebruikt om gegevens te herstellen. Ga voor meer informatie over het maken en beheren van back-upbeleid naar [De StorSimple Device Manager-service gebruiken om back-upbeleid te beheren.](storsimple-8000-manage-backup-policies-u2.md)

## <a name="common-considerations-for-device-failover"></a>Algemene overwegingen voor apparaatfailover

Controleer de volgende informatie voordat u niet over een apparaat slaagt:

* Voordat een apparaatfailover wordt gestart, moeten alle volumes binnen de volumecontainers offline zijn. In een ongeplande failover gaan StotSimple-volumes automatisch offline. Maar als u een geplande failover uitvoert (om DR te testen), moet u alle volumes offline halen.
* Alleen de volumecontainers met een bijbehorende cloudmomentopname worden weergegeven voor DR. Er moet ten minste één volumecontainer met een bijbehorende cloudmomentopname zijn om gegevens te herstellen.
* Als er cloudmomentopnamen zijn die zich uitstrekken over meerdere volumecontainers, mislukt StorSimple over deze volumecontainers als set. In een zeldzame instantie, als er lokale momentopnamen zijn die zich uitstrekken over meerdere volumecontainers, maar bijbehorende cloudmomentopnamen dit niet doen, mislukt StorSimple niet over de lokale momentopnamen en gaan de lokale gegevens verloren na DR.
* De beschikbare doelapparaten voor DR zijn apparaten die voldoende ruimte hebben om de geselecteerde volumecontainers te kunnen verwerken. Apparaten die niet voldoende ruimte hebben, worden niet vermeld als doelapparaten.
* Na een DR (voor een beperkte duur) kunnen de prestaties van de gegevenstoegang aanzienlijk worden beïnvloed, omdat het apparaat toegang moet krijgen tot de gegevens vanuit de cloud en deze lokaal moet opslaan.

#### <a name="device-failover-across-software-versions"></a>Apparaatfailover in softwareversies

Een StorSimple Device Manager-service in een implementatie kan meerdere apparaten hebben, zowel fysiek als in de cloud, allemaal met verschillende softwareversies.

Gebruik de volgende tabel om te bepalen of u mislukken of niet terug naar een ander apparaat waarop een andere softwareversie wordt uitgevoerd en hoe de volumetypen zich gedragen tijdens DR.

#### <a name="failover-and-failback-across-software-versions"></a>Failover en failback in verschillende softwareversies

| Fail over/ Fail back from | Fysiek apparaat | Cloudapparaat |
| --- | --- | --- |
| Update 3 naar Update 4 |Gelaagde volumes mislukken als trapsgewijs. <br></br>Lokaal vastgemaakte volumes mislukken als lokaal vastgemaakt. <br></br> Na een failover wanneer u een momentopname maakt op het update 4-apparaat, wordt [op heatmap gebaseerde tracking](storsimple-update4-release-notes.md#whats-new-in-update-4) ingeschakeld. |Lokaal vastgemaakte volumes mislukken als trapsgewijs. |
| Update 4 naar Update 3 |Gelaagde volumes mislukken als trapsgewijs. <br></br>Lokaal vastgemaakte volumes mislukken als lokaal vastgemaakt. <br></br> Back-ups die worden gebruikt om heatmap-metagegevens te herstellen, behouden. <br></br>Heatmap-tracking is niet beschikbaar in update 3 na een failback. |Lokaal vastgemaakte volumes mislukken als trapsgewijs. |


## <a name="device-failover-scenarios"></a>Scenario's voor apparaatfailover

Als er een ramp is, u ervoor kiezen om te falen op uw StorSimple-apparaat:

* [Naar een fysiek apparaat](storsimple-8000-device-failover-physical-device.md).
* [Aan zichzelf.](storsimple-8000-device-failover-same-device.md)
* [Naar een cloudtoestel.](storsimple-8000-device-failover-cloud-appliance.md)

De voorgaande artikelen bieden gedetailleerde stappen voor elk van de bovenstaande failover-aanvragen.


## <a name="failback"></a>Failback

Voor update 3- en latere versies ondersteunt StorSimple ook failback. Failback is slechts het omgekeerde van failover, het doel wordt de bron en de oorspronkelijke bron apparaat tijdens de failover wordt nu het doelapparaat. 

Tijdens failback synchroniseert StorSimple de gegevens opnieuw naar de primaire locatie, stopt de I/O- en toepassingsactiviteit en gaat het terug naar de oorspronkelijke locatie.

Nadat een failover is voltooid, voert StorSimple de volgende acties uit:

* StorSimple reinigt de volumecontainers die zijn mislukt vanaf het bronapparaat.
* StorSimple initieert een achtergrondtaak per volumecontainer (mislukt) op het bronapparaat. Als u probeert terug te mislukken terwijl de taak aan de gang is, ontvangt u een melding met die strekking. Wacht tot de taak is voltooid om de failback te starten.
* De tijd die nodig is om het verwijderen van volumecontainers te voltooien, is afhankelijk van verschillende factoren, zoals de hoeveelheid gegevens, de leeftijd van de gegevens, het aantal back-ups en de netwerkbandbreedte die beschikbaar is voor de bewerking.

Als u testfailovers plant of failbacks test, raden we u aan volumecontainers met minder gegevens (Gbs) te testen. Meestal u de failback 24 uur nadat de failover is voltooid starten.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

V. **Wat gebeurt er als de DR faalt of gedeeltelijk succes heeft?**

A. Als de DR mislukt, raden we u aan het opnieuw te proberen. De failovertaak van het tweede apparaat is zich bewust van de voortgang van de eerste taak en begint vanaf dat punt.

V. **Kan ik een apparaat verwijderen terwijl de failover van het apparaat aan de gang is?**

A. U een apparaat niet verwijderen terwijl een DR aan de gang is. U uw apparaat alleen verwijderen nadat de DR is voltooid. U de voortgang van de failovertaak van het apparaat controleren in het blade **Jobs.**

V. **Wanneer start de garbage collection op het bronapparaat, zodat de lokale gegevens op het bronapparaat worden verwijderd?**

A. Garbage collection is ingeschakeld op het bronapparaat pas nadat het apparaat volledig is schoongemaakt. De opschoning omvat het opruimen van objecten die zijn mislukt vanaf het bronapparaat, zoals volumes, back-upobjecten (geen gegevens), volumecontainers en beleidsregels.

V. **Wat gebeurt er als de verwijdertaak die is gekoppeld aan de volumecontainers in het bronapparaat mislukt?**

A.  Als de verwijderingstaak mislukt, u de volumecontainers handmatig verwijderen. Selecteer **in** het blade Apparaten uw bronapparaat en klik op **Volumecontainers**. Selecteer de volumecontainers die u niet hebt over en onder aan het blad klikt op **Verwijderen**. Nadat u alle defecte inhoudscontainers op het bronapparaat hebt verwijderd, u de failback starten. Ga voor meer informatie naar [Een volumecontainer verwijderen](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business continuity disaster recovery (BCDR)

Een BCDR-scenario (Business Continuity disaster recovery) treedt op wanneer het hele Azure-datacenter niet meer functioneert. Dit scenario kan van invloed zijn op uw StorSimple Device Manager-service en de bijbehorende StorSimple-apparaten.

Als een StorSimple-apparaat is geregistreerd vlak voordat een ramp zich voordeed, moet dit apparaat mogelijk een fabrieksreset ondergaan. Na de ramp wordt het StorSimple-apparaat als offline weergegeven in de Azure-portal. Dit apparaat moet van de portal worden verwijderd. Stel het apparaat terug naar fabrieksstandaardinstellingen en registreer het opnieuw bij de service.

## <a name="next-steps"></a>Volgende stappen

Als u klaar bent om een apparaatfailover uit te voeren, kiest u een van de volgende scenario's voor gedetailleerde instructies:

- [Overgaan naar een ander fysiek apparaat](storsimple-8000-device-failover-physical-device.md)
- [Overgaan naar hetzelfde apparaat](storsimple-8000-device-failover-same-device.md)
- [Overgaan naar StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Als het niet lukt ten opzichte van uw apparaat, kiest u uit een van de volgende opties:

* [Deactiveer of verwijder uw StorSimple-apparaat.](storsimple-8000-deactivate-and-delete-device.md)
* [Gebruik de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

