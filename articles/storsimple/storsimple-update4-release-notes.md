---
title: StorSimple 8000 Series Update 4 release notes | Microsoft Documenten
description: Beschrijft de nieuwe functies, problemen en oplossingen voor StorSimple 8000 Series Update 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254584"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 Series Update 4 release notes

## <a name="overview"></a>Overzicht

De volgende release notes beschrijven de nieuwe functies en identificeren de kritieke open problemen voor StorSimple 8000 Series Update 4. Ze bevatten ook een lijst van de StorSimple software-updates opgenomen in deze release. 

Update 4 kan worden toegepast op elk StorSimple-apparaat met Release (GA) of Update 0.1 tot en met update 3.1. De apparaatversie die is gekoppeld aan Update 4 is 6.3.9600.17820.

Bekijk de informatie in de releasenotes voordat u de update implementeert in uw StorSimple-oplossing.

> [!IMPORTANT]
> * Update 4 heeft apparaatsoftware, USM-firmware, LSI-stuurprogramma en firmware, schijffirmware, Storport en Spaceport, beveiliging en andere OS-updates. Het installeren van deze update duurt ongeveer 4 uur. Disk firmware update is een storende update en resulteert in een downtime voor uw apparaat. We raden u aan update 4 toe te passen om uw apparaat up-to-date te houden. 
> * Voor nieuwe releases ziet u mogelijk niet meteen updates omdat we een gefaseerde implementatie van de updates uitvoeren. Wacht een paar dagen, en dan scannen voor updates weer als deze binnenkort beschikbaar zal komen.

## <a name="whats-new-in-update-4"></a>Nieuwe informatie in Update 4

De volgende belangrijke verbeteringen en bug fixes zijn gemaakt in Update 4.

* **Slimmere geautomatiseerde ruimteterugwinningsalgoritmen** - In update 4 worden de geautomatiseerde reclamatiealgoritmen voor ruimteterugwinning verbeterd om de ruimteterugwinningscycli aan te passen op basis van de verwachte teruggewonnen ruimte die beschikbaar is in de cloud. 

* **Prestatieverbeteringen voor lokaal vastgemaakte volumes** – Update 4 heeft de prestaties van lokaal vastgemaakte volumes verbeterd in scenario's met een hoge gegevensopname (gegevens die vergelijkbaar zijn met de grootte van het volume).

* **Heatmap-gebaseerde herstel** - In de eerdere releases, na een disaster recovery (DR), werden de gegevens hersteld uit de cloud op basis van de toegangspatronen die resulteren in een trage prestaties. 

    Een nieuwe functie wordt geïmplementeerd in Update 4 die vaak geopende gegevens bijhoudt om een heatmap te maken wanneer het apparaat in gebruik is voorafgaand aan DR (De meeste gebruikte gegevensbrokken hebben een hoog vuur, terwijl minder gebruikte brokken weinig warmte hebben). Na DR gebruikt StorSimple de heatmap om de gegevens uit de cloud automatisch te herstellen en te rehydrateren. 

    Alle herstelt zijn nu heatmap gebaseerd herstelt. Ga voor meer informatie over het opvragen en annuleren van heatmap-gebaseerde herstel- en rehydratietaken naar [Windows PowerShell voor StorSimple-cmdlet-verwijzing.](https://technet.microsoft.com/library/dn688168.aspx)

* **StorSimple Diagnostics tool** - In Update 4, een StorSimple Diagnostics tool wordt vrijgegeven om eenvoudige diagnose en het oplossen van problemen met betrekking tot het systeem, netwerk, prestaties, en hardware component gezondheid. Deze tool wordt uitgevoerd via de Windows PowerShell voor StorSimple. Ga voor meer informatie naar [probleemproblemen met het hulpprogramma StorSimple Diagnostics.](storsimple-8000-diagnostics.md)

* **UI-gebaseerde StorSimple-migratietool** - Voordat deze release werd uitgevoerd, moesten de gebruikers een deel van de migratieworkflow uitvoeren met behulp van de Azure PowerShell-interface. In deze release wordt een gebruiksvriendelijke UI-gebaseerde StorSimple Migration-tool beschikbaar gesteld voor ondersteuning om dezelfde migratieworkflow te vergemakkelijken. Deze tool zou ook zorgen voor de consolidatie van herstel emmers. 

* **FIPS-gerelateerde wijzigingen** - Deze release is vanaf nu standaard ingeschakeld op alle Apparaten uit de StorSimple 8000-serie voor zowel de openbare cloudaccounts van Microsoft Azure Government als Azure.

* **Wijzigingen bijwerken** - In deze release zijn bugs met betrekking tot updatefouten opgelost.

* **Waarschuwing voor schijffouten** - In deze versie wordt een nieuwe waarschuwing toegevoegd die de gebruiker waarschuwt voor dreigende schijfstoringen. Als u deze waarschuwing tegenkomt, neemt u contact op met Microsoft Support om een vervangende schijf te verzenden. Ga voor meer informatie naar [hardwaremeldingen op uw StorSimple-apparaat.](storsimple-8000-manage-alerts.md#hardware-alerts)

* **Wijzigingen voor de vervanging** van de controller - In deze versie wordt een cmdlet toegevoegd waarmee de gebruiker de status van het vervangingsproces van de controller kan opvragen. Ga voor meer informatie naar de [cmdlet om de vervangingsstatus van de controller op te vragen.](https://technet.microsoft.com/library/dn688168.aspx)


## <a name="issues-fixed-in-update-4"></a>Problemen opgelost in Update 4

In de volgende tabel vindt u een overzicht van problemen die zijn opgelost in update 4.    

| Nee | Functie | Probleem | Van toepassing op fysiek apparaat | Van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Failover |In de eerdere release, na de failover, was er een probleem met betrekking tot het opruimen waargenomen op de site van de klant. Dit probleem is opgelost in deze release. |Ja |Ja |
| 2 |Lokaal vastgemaakte volumes |In de vorige release was er een probleem met de bijbehorende volumecreatie voor lokaal vastgemaakte volumes die zouden resulteren in fouten bij het maken van volumes. Dit probleem is veroorzaakt door de wortel en is opgelost in deze release. |Ja |Nee |
| 3 |Ondersteuningspakket |In eerdere versie waren er problemen met betrekking tot het ondersteuningspakket die zouden resulteren in een System.OutOfMemory-uitzondering of andere fouten die resulteren in een fout bij het maken van een ondersteuningspakket. Deze bugs zijn opgelost in deze release. |Ja |Ja |
| 4 |Bewaking |In de vorige versie was er een probleem met betrekking tot het bewaken van grafieken voor lokaal vastgemaakte volumes waarbij het verbruik werd weergegeven in EB. Deze bug is opgelost in deze release. |Ja |Ja |
| 5 |Migratie |In de vorige versie waren er verschillende problemen met betrekking tot de betrouwbaarheid van migratie van 5000-7000-serie naar 8000-serie apparaten. Deze problemen zijn in deze release verholpen. |Ja |Ja |
| 6 |Update |In eerdere versies, als er een update fout, de controllers zou gaan in herstelmodus en dus de gebruiker niet kon doorgaan met de update en zou moeten contact opnemen met Microsoft Support. <br> Dit gedrag is veranderd in deze release. Als de gebruiker een updatefout heeft nadat beide controllers dezelfde versie uitvoeren (Update 4), gaan de controllers niet in de herstelmodus. Als de gebruiker deze fout tegenkomt, raden we aan om een beetje te wachten en de update opnieuw te proberen. De nieuwe poging zou kunnen slagen. Als de nieuwe poging mislukt, moeten ze contact opnemen met Microsoft Support. |Ja |Ja |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Bekende problemen in update 4 van eerdere releases

Er zijn geen nieuwe bekende problemen in update 4. Ga naar [Update 3 release notes](storsimple-update3-release-notes.md#known-issues-in-update-3)voor een lijst met problemen die zijn overgedragen naar update 4 van eerdere releases.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>SCSI-controller en firmware-updates (Serial Attached SCSI) en firmware in update 4

Deze release heeft SAS controller en LSI driver en firmware-updates. Zie [Update 4 installeren](storsimple-install-update-4.md) op uw StorSimple-apparaat voor meer informatie over het installeren van deze updates.

## <a name="virtual-device-updates-in-update-4"></a>Updates van virtuele apparaten in update 4

Deze update kan niet worden toegepast op het StorSimple Cloud Appliance (ook wel het virtuele apparaat genoemd). Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap

Meer informatie over het [installeren van Update 4](storsimple-install-update-4.md) op uw StorSimple-apparaat.

