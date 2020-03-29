---
title: StorSimple Device Manager-servicebeheer | Microsoft Documenten
description: Lees hoe u uw StorSimple-apparaat beheert met de StorSimple Device Manager-service in de Azure-portal.
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
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723303"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Gebruik de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren

## <a name="overview"></a>Overzicht

In dit artikel wordt de Serviceinterface van StorSimple Device Manager beschreven, inclusief hoe u verbinding maken met de verschillende beschikbare opties en koppelingen naar de specifieke workflows die via deze gebruikersinterface kunnen worden uitgevoerd. Deze leidraad is van toepassing op beide; het fysieke apparaat StorSimple en het cloudtoestel.

Na het lezen van dit artikel leer je:

* Verbinding maken met de StorSimple Device Manager-service
* Beheer uw StorSimple-apparaat via de StorSimple Device Manager-service

## <a name="connect-to-storsimple-device-manager-service"></a>Verbinding maken met de StorSimple Device Manager-service

De StorSimple Device Manager-service wordt uitgevoerd in Microsoft Azure en maakt verbinding met meerdere StorSimple-apparaten. U gebruikt een centrale Microsoft Azure-portal die in een browser wordt uitgevoerd om deze apparaten te beheren. Ga als volgt te werk om verbinding te maken met de StorSimple Device Manager-service.

#### <a name="to-connect-to-the-service"></a>Verbinding maken met de service
1. Navigeer [https://portal.azure.com/](https://portal.azure.com/)naar .
2. Meld u met uw Microsoft-accountreferenties aan bij de Microsoft Azure-portal (rechtsboven in het deelvenster).
3. Schuif naar beneden in het linkernavigatiedeelvenster om toegang te krijgen tot de StorSimple Device Manager-service.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple-apparaat beheren met de StorSimple Device Manager-service

In de volgende tabel vindt u een overzicht van alle algemene beheertaken en complexe werkstromen die kunnen worden uitgevoerd binnen de gebruikersinterface van de StorSimple Device Manager-service. Deze taken worden georganiseerd op basis van de UI-blades waarop ze worden gestart.

Klik op de juiste procedure in de tabel voor meer informatie over elke werkstroom.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple-werkstromen voor apparaatbeheer

| Als u dit wilt doen ... | Gebruik deze procedure. |
| --- | --- |
| Een service maken</br>Een service verwijderen</br>Serviceregistratiesleutel ophalen</br>Serviceregistratiesleutel regenereren |[Een StorSimple Device Manager-service implementeren](storsimple-8000-manage-service.md) |
| De activiteitslogboeken weergeven |[Het overzicht van de StorSimple Device Manager-service gebruiken](storsimple-8000-service-dashboard.md) |
| De versleutelingssleutel voor servicegegevens wijzigen</br>De bewerkingslogboeken weergeven |[Het servicedashboard van StorSimple Device Manager gebruiken](storsimple-8000-service-dashboard.md) |
| Een apparaat deactiveren</br>Een apparaat verwijderen |[Een apparaat deactiveren of verwijderen](storsimple-8000-deactivate-and-delete-device.md) |
| Meer informatie over noodherstel en apparaatfailover</br>Failover naar een fysiek apparaat</br>Failover naar een virtueel apparaat</br>Business continuity disaster recovery (BCDR) |[Failover en disaster recovery voor uw StorSimple-apparaat](storsimple-8000-device-failover-disaster-recovery.md) |
| Back-ups voor een volume weergeven</br>Een back-upset selecteren</br>Een back-upset verwijderen |[Back-ups beheren](storsimple-8000-manage-backup-catalog.md) |
| Een volume klonen |[Een volume klonen](storsimple-8000-clone-volume-u2.md) |
| Een back-upset herstellen |[Een back-upset herstellen](storsimple-8000-restore-from-backup-set-u2.md) |
| Over opslagaccounts</br>Een opslagaccount toevoegen</br>Een opslagaccount bewerken</br>Een opslagaccount verwijderen</br>Sleutelrotatie van opslagrekeningen |[Opslagaccounts beheren](storsimple-8000-manage-storage-accounts.md) |
| Informatie over bandbreedtesjablonen</br>Een bandbreedtesjabloon toevoegen</br>Een bandbreedtesjabloon bewerken</br>Een bandbreedtesjabloon verwijderen</br>Een standaardbandbreedtesjabloon gebruiken</br>Een bandbreedtesjabloon maken die op een bepaald tijdstip begint |[Bandbreedtesjablonen beheren](storsimple-8000-manage-bandwidth-templates.md) |
| Informatie over toegangscontrolerecords</br>Een toegangscontrolerecord maken</br>Een toegangscontrolerecord bewerken</br>Een toegangscontrolerecord verwijderen |[Toegangsbeheerrecords beheren](storsimple-8000-manage-acrs.md) |
| Taakdetails weergeven</br>Een taak annuleren |[Taken beheren](storsimple-8000-manage-jobs-u2.md) |
| Waarschuwingen ontvangen</br>Waarschuwingen beheren</br>Waarschuwingen bekijken |[StorSimple-waarschuwingen weergeven en beheren](storsimple-8000-manage-alerts.md) |
| Bewakingsdiagrammen maken |[Uw StorSimple-apparaat bewaken](storsimple-monitor-device.md) |
| Een volumecontainer toevoegen</br>Een volumecontainer wijzigen</br>Een volumecontainer verwijderen |[Volumecontainers beheren](storsimple-8000-manage-volume-containers.md) |
| Een volume toevoegen</br>Een volume wijzigen</br>Een volume offline halen</br>Een volume verwijderen</br>Een volume controleren |[Volumes beheren](storsimple-8000-manage-volumes-u2.md) |
| Apparaatinstellingen wijzigen</br>Tijdinstellingen wijzigen</br>DNS.md-instellingen wijzigen</br>Netwerkinterfaces configureren |[Apparaatconfiguratie voor uw StorSimple-apparaat wijzigen](storsimple-8000-modify-device-config.md) |
| Webproxy-instellingen weergeven |[Webproxy configureren voor uw apparaat](storsimple-8000-configure-web-proxy.md) |
| Wachtwoord apparaatbeheerder wijzigen</br>Wachtwoord Voor StorSimple Snapshot Manager wijzigen |[StorSimple-wachtwoorden wijzigen](storsimple-8000-change-passwords.md) |
| Extern beheer configureren |[Op afstand verbinding maken met uw StorSimple-apparaat](storsimple-8000-remote-connect.md) |
| Waarschuwingsinstellingen configureren |[StorSimple-waarschuwingen weergeven en beheren](storsimple-8000-manage-alerts.md) |
| CHAP configureren voor uw StorSimple-apparaat |[CHAP configureren voor uw StorSimple-apparaat](storsimple-configure-chap.md) |
| Een back-upbeleid toevoegen</br>Een schema toevoegen of wijzigen</br>Een back-upbeleid verwijderen</br>Neem een handmatige back-up</br>Een aangepast back-upbeleid maken met meerdere volumes en planningen |[Back-upbeleid beheren](storsimple-8000-manage-backup-policies-u2.md) |
| Apparaatcontrollers stoppen</br>Apparaatcontrollers opnieuw opstarten</br>Apparaatcontrollers uitschakelen</br>Uw apparaat opnieuw instellen op fabrieksstandaardinstellingen</br>(Hierboven zijn alleen voor on-premises apparaat) |[StorSimple-apparaatcontroller beheren](storsimple-8000-manage-device-controller.md) |
| Meer informatie over StorSimple-hardwarecomponenten</br>Hardwarestatus controleren</br>(Hierboven zijn alleen voor on-premises apparaat) |[Hardwarecomponenten controleren](storsimple-8000-monitor-hardware-status.md) |
| Een ondersteuningspakket maken |[Een ondersteuningspakket maken en beheren](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Software-updates installeren |[Uw apparaat bijwerken](storsimple-update-device.md) |

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met de dagelijkse werking van uw StorSimple-apparaat of met een van de hardwarecomponenten, raadpleegt u:

* [Problemen oplossen met het gereedschap Diagnostische gegevens](storsimple-8000-diagnostics.md)
* [StorSimple-bewakingsindicator-LED's gebruiken](storsimple-monitoring-indicators.md)

Als u de problemen niet oplossen en u een serviceaanvraag moet maken, raadpleegt u [Contact opnemen met Microsoft Support](storsimple-8000-contact-microsoft-support.md).

