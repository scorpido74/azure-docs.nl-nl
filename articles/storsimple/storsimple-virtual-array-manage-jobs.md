---
title: StorSimple Virtual Array-taken weergeven en beheren | Microsoft Documenten
description: Beschrijft de pagina Taken van StorSimple Device Manager-service en hoe u deze gebruiken om recente en huidige taken voor de Virtuele Array van StorSimple bij te houden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: dbab2aaab2c12bef07748f54e5864d042f1c982a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60302477"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>De StorSimple Device Manager-service gebruiken om taken voor de Virtuele Array van StorSimple weer te geven
## <a name="overview"></a>Overzicht
Het **blade Jobs** biedt één centrale portal voor het bekijken en beheren van taken die zijn gestart op virtuele arrays die zijn verbonden met uw StorSimple Device Manager-service. U uitvoeren, voltooid en mislukte taken voor meerdere virtuele apparaten weergeven. De resultaten worden in een tabelvormige indeling weergegeven.

![Het blad van de Banen](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

U snel de vacatures vinden waarin u geïnteresseerd bent door te filteren op velden zoals:

* **Tijdbereik** : taken kunnen worden gefilterd op basis van de datum en het tijdsbereik.
* **Apparaten** : er worden taken gestart op een specifiek apparaat dat is aangesloten op uw service. De gefilterde taken worden vervolgens in tabelvorm weergegeven op basis van de volgende kenmerken:
  
  * **Naam** - De naam van de taak kan **alle**, **back-up**, **Kloon**, **Fail over**, **Download updates,** of **Updates installeren**.
  * **Status** : Taken kunnen **alles**zijn, **in uitvoering,** **geslaagd**of **mislukt**of **geannuleerd**.
  * **Entiteit** : de taken kunnen worden gekoppeld aan een volume, aandeel of apparaat.
  * **Apparaat** : de naam van het apparaat waarop de taak is gestart.
  * **Begonnen op** – De tijd dat de taak werd gestart.
  * **Duur** : de duur waarvoor de taak is uitgevoerd.
* **Status** : u zoeken naar alle taken, het uitvoeren, voltooien of mislukte taken.
* **Taaktype** : het taaktype kan alles zijn, een back-up maken, herstellen, mislukken, updates downloaden of updates installeren.

De lijst met taken wordt elke 30 seconden vernieuwd.

## <a name="view-job-details"></a>Taakdetails weergeven
Voer de volgende stappen uit om de details van een taak weer te geven.

#### <a name="to-view-job-details"></a>Taakgegevens bekijken
1. Geef in het blad **Vacatures** de taak(en) weer waarin u geïnteresseerd bent door een query met de juiste filters uit te voeren. U zoeken naar voltooide of lopende taken.
2. Selecteer een taak in de tabellijst met taken.
   
    ![Het blad van de baan](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Klik onder aan de pagina op **Details**.
4. In het dialoogvenster **Details** u status- en details- en tijdstatistieken weergeven. In de volgende afbeelding ziet u een voorbeeld van het dialoogvenster **Back-uptaakgegevens.**
   
    ![Taakdetails](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Taakfouten wanneer de virtuele machine wordt onderbroken in de hypervisor
Wanneer een taak aan de gang is op uw StorSimple Virtual Array en het apparaat (virtuele machine ingericht in hypervisor) wordt onderbroken voor meer dan 15 minuten, de taak mislukt. Dit is te wijten aan uw StorSimple Virtual Array tijd wordt niet synchroon met de Microsoft Azure tijd. 

U ziet de volgende fout: "De tijd van uw apparaat is niet meer dan 15 minuten gesynchroniseerd met de Microsoft Azure-tijd. Zorg ervoor dat de hypervisor en de apparaattijden worden gesynchroniseerd met een NTP-server. Controleer of er geen verbindingsproblemen zijn. Als u verbindingsproblemen wilt oplossen, voert u diagnostische tests uit vanaf de lokale webgebruikersinterface van uw virtuele apparaat."

Deze fouten zijn van toepassing op back-up-, herstel-, update- en failovertaken. Als uw virtuele machine is ingericht in Hyper-V, synchroniseert de machine uiteindelijk de tijd met uw hypervisor. Zodra dat gebeurt, u uw baan opnieuw opstarten.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het gebruik van de lokale web-gebruikersinterface om uw StorSimple Virtual Array te beheren.](storsimple-ova-web-ui-admin.md)

