---
title: StorSimple 8000 Series Update 5 release notes
description: Beschrijft de nieuwe functies, problemen en oplossingen voor StorSimple 8000 Series Update 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275157"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 Series Update 5 release notes

## <a name="overview"></a>Overzicht

De volgende release notes beschrijven de nieuwe functies en identificeren de kritieke open problemen voor StorSimple 8000 Series Update 5. Ze bevatten ook een lijst van de StorSimple software-updates opgenomen in deze release.

Update 5 kan worden toegepast op elk StorSimple-apparaat met Update 0.1 tot en met update 4. De apparaatversie die is gekoppeld aan Update 5 is 6.3.9600.17845.

Bekijk de informatie in de releasenotes voordat u de update implementeert in uw StorSimple-oplossing.

> [!IMPORTANT]
> * Update 5 is een verplichte update en moet onmiddellijk worden geïnstalleerd. Zie voor meer informatie hoe [u Update 5 toepast](storsimple-8000-install-update-5.md).
> * Update 5 bevat apparaatsoftware, schijffirmware, besturingssysteembeveiliging en andere OS-updates. Het installeren van deze update duurt ongeveer 4 uur. Disk firmware update is een storende update en resulteert in een downtime voor uw apparaat. We raden u aan Update 5 toe te passen om uw apparaat up-to-date te houden.
> * Voor nieuwe releases ziet u mogelijk niet meteen updates omdat we een gefaseerde implementatie van de updates uitvoeren. Wacht een paar dagen en scan vervolgens opnieuw naar updates, omdat deze updates binnenkort beschikbaar zullen zijn.

## <a name="whats-new-in-update-5"></a>Nieuwe informatie in Update 5

De volgende belangrijke verbeteringen en bug fixes zijn gemaakt in Update 5.

* **Gebruik van Azure Active Directory (AAD) om te verifiëren met de StorSimple Device Manager-service** : Vanaf update 5 wordt Azure Active Directory gebruikt om te verifiëren met de StorSimple Device Manager-service. Het oude verificatiemechanisme wordt in december 2017 afgeschaft. Alle gebruikers moeten de nieuwe verificatie-URL's opnemen in hun firewallregels. Ga voor meer informatie naar [verificatie-URL's die worden vermeld in de netwerkvereisten voor uw StorSimple-apparaat.](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal)

    Als de url van de verificatie niet is opgenomen in de firewallregels, zien de gebruikers een kritieke waarschuwing dat hun StorSimple-apparaat zich niet kan verifiëren met de service. Als de gebruikers deze waarschuwing zien, moeten ze de nieuwe verificatie-URL toevoegen. Ga voor meer informatie naar [StorSimple-netwerkwaarschuwingen.](storsimple-8000-manage-alerts.md#networking-alerts)

* **Nieuwe versie van StorSimple Snapshot Manager** - Een nieuwe versie van StorSimple Snapshot Manager wordt uitgebracht met Update 5 en is compatibel met alle StorSimple-apparaten die Update 4 of hoger uitvoeren. We raden u aan deze versie bij te werken. De vorige versie van StorSimple Snapshot Manager wordt gebruikt voor StorSimple-apparaten waarop Update 3 of eerder wordt uitgevoerd. [Download de juiste versie van StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) en raadpleeg [StorSimple Snapshot Manager.](storsimple-snapshot-manager-deployment.md)


## <a name="issues-fixed-in-update-5"></a>Problemen opgelost in Update 5

In de volgende tabel vindt u een overzicht van problemen die zijn opgelost in update 5.

| Nee | Functie | Probleem | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell remoting |In de vorige versie zou een gebruiker een fout ontvangen terwijl hij probeert een externe verbinding met het StorSimple Cloud Appliance via Windows PowerShell tot stand te brengen. Dit probleem is veroorzaakt door de wortel en is opgelost in deze release. |Nee |Ja |
| 2 |Bandbreedtesjablonen |In eerdere versie was er een probleem met bandbreedtesjablonen die resulteerden in een lagere bandbreedte dan waarvoor het apparaat was geconfigureerd. Dit probleem is opgelost in deze release. |Ja |Ja |
| 3 |Failover |In eerdere versie, wanneer een apparaat met een groot aantal volumes niet is overgegaan naar een ander apparaat met update 4, mislukt het proces wanneer het apparaat de toegangscontrolerecords probeert toe te passen. Dit probleem is opgelost in deze release. |Ja |Ja |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Bekende problemen in update 5 van eerdere releases

Er zijn geen nieuwe bekende problemen in Update 5. Ga naar [Update 3 release notes](storsimple-update3-release-notes.md#known-issues-in-update-3)voor een lijst met problemen die zijn overgedragen naar update 5 van eerdere releases.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>SCSI-controller en firmware-updates (Serial Attached SCSI) en firmware in Update 5

Deze release heeft SAS controller en LSI driver en firmware-updates. Zie [Update 5 installeren](storsimple-8000-install-update-5.md) op uw StorSimple-apparaat voor meer informatie over het installeren van deze updates.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>StorSimple Cloud Appliance updates in Update 5

Deze update kan niet worden toegepast op het StorSimple Cloud Appliance (ook wel het virtuele apparaat genoemd). Er moeten nieuwe cloudapparaten worden gemaakt met de afbeelding Update 5. Ga voor informatie over het maken van een StorSimple Cloud Appliance naar [Implementeren en beheren van een StorSimple Cloud Appliance.](storsimple-8000-cloud-appliance-u2.md)

## <a name="next-step"></a>Volgende stap

Meer informatie over het [installeren van Update 5](storsimple-8000-install-update-5.md) op uw StorSimple-apparaat.

