---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176231"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Updates voor onderhoudsmodus installeren via Windows PowerShell voor StorSimple

Wanneer u updates over de onderhoudsmodus toepast op het StorSimple-apparaat, worden alle I/O-aanvragen onderbroken. Services zoals niet-vluchtig random access memory (NVRAM) of de clusterservice worden gestopt. Beide controllers starten opnieuw op wanneer u deze modus in- of uitsluit. Wanneer u deze modus afsluit, worden alle services hervat en zijn ze in orde. (Dit kan enkele minuten duren.)

> [!IMPORTANT]
> * Controleer voordat u de onderhoudsmodus invoert of beide apparaatcontrollers in de Azure-portal in orde zijn. Als de controller niet in orde is, [neemt u contact op met Microsoft Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) voor de volgende stappen.
> * Wanneer u in de onderhoudsmodus bent, moet u eerst de ene controller en vervolgens de andere controller bijwerken.

1. Gebruik PuTTY om verbinding te maken met de seriële console. Volg de gedetailleerde instructies in [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Druk op de opdrachtregel op **Enter**. Selecteer Optie 1, **Log in met volledige toegang**.

2. Als u de controller in de onderhoudsmodus wilt plaatsen, typt u:
    
    `Enter-HcsMaintenanceMode`

    Beide controllers starten opnieuw in de onderhoudsmodus.

3. Installeer uw onderhoudsmodusupdates. Type:

    `Start-HcsUpdate`

    U wordt om bevestiging gevraagd. Nadat u de updates hebt bevestigd, worden ze geïnstalleerd op de controller die u momenteel hebt geopend. Nadat de updates zijn geïnstalleerd, wordt de controller opnieuw opgestart.

4. Controleer de status van updates. Meld u aan bij de peer controller terwijl de huidige controller wordt bijgewerkt en kan geen andere opdrachten verwerken. Type:

    `Get-HcsUpdateStatus`

    Als `RunInProgress` de `True`is, de update is nog in volle gang. Als `RunInProgress` `False`dat zo is, geeft dit aan dat de update is voltooid.

5. Nadat de firmware-updates van de schijf zijn toegepast en de bijgewerkte controller opnieuw is opgestart, controleert u de versie van de schijffirmware. Typ op de bijgewerkte controller:

    `Get-HcsFirmwareVersion`
   
    De verwachte versies van de schijffirmware zijn:`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Verlaat de onderhoudsmodus. Typ de volgende opdracht voor elke apparaatcontroller:

    `Exit-HcsMaintenanceMode`

    De controllers starten opnieuw op wanneer u de onderhoudsmodus afsluit.

7. Ga terug naar de Azure-portal. De portal mag niet laten zien dat u de onderhoudsmodus updates voor 24 uur hebt geïnstalleerd.