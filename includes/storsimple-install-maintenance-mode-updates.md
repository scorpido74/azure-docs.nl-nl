---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176070"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Updates voor de onderhoudsmodus installeren via Windows PowerShell voor StorSimple
1. Als u dit nog niet hebt gedaan, gaat u naar de seriële console van het apparaat en selecteert u optie 1, **Log in met volledige toegang**. 
2. Typ het wachtwoord. Het standaardwachtwoord is **Password1**.
3. Typ het volgende na de opdrachtprompt:
   
     `Get-HcsUpdateAvailability` 
4. U krijgt een melding of er updates beschikbaar zijn en of de updates storend of niet-storend zijn. Als u storende updates wilt toepassen, moet u het apparaat in de onderhoudsmodus plaatsen. Zie [Stap 2: Voer de onderhoudsmodus](../articles/storsimple/storsimple-update-device.md#step2) in voor instructies.
5. Wanneer uw apparaat in de onderhoudsmodus staat, typt u bij de opdrachtprompt:`Start-HcsUpdate`
6. U wordt gevraagd om een bevestiging. Nadat u de updates hebt bevestigd, worden ze geïnstalleerd op de controller die u momenteel bezoekt. Nadat de updates zijn geïnstalleerd, wordt de controller opnieuw opgestart. 
7. Controleer de status van updates. Type:
   
    `Get-HcsUpdateStatus`
   
    Als `RunInProgress` de `True`is, de update is nog in volle gang. Als `RunInProgress` `False`dat zo is, geeft dit aan dat de update is voltooid.  
8. Wanneer de update is geïnstalleerd op de huidige controller en deze opnieuw is opgestart, maakt u verbinding met de andere controller en voert u stappen 1 tot en met 6 uit.
9. Nadat beide controllers zijn bijgewerkt, wordt de onderhoudsmodus afgesloten. Zie [Stap 4: Onderhoudsmodus afsluiten](../articles/storsimple/storsimple-update-device.md#step4) voor instructies.

