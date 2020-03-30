---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176065"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Hotfixes in de onderhoudsmodus installeren via Windows PowerShell voor StorSimple
> [!IMPORTANT]
> In de onderhoudsmodus moet u de hotfix eerst op één controller en vervolgens op de andere controller toepassen.
> 
> 

1. Plaats het apparaat in de onderhoudsmodus. Zie [Stap 2: Voer de onderhoudsmodus in](../articles/storsimple/storsimple-update-device.md#step2) voor instructies over het ingaan van de onderhoudsmodus.
2. Als u de hotfix wilt toepassen, typt u het:
   
     `Start-HcsHotfix` 
3. Lever het pad op de plaats wanneer u daarom wordt gevraagd, aan de gedeelde netwerkmap met de hotfix-bestanden.
4. U wordt gevraagd om een bevestiging. Type **Y** om verder te gaan met de hotfix-installatie.
5. Nadat u de hotfix op één controller hebt toegepast, meldt u zich aan bij de andere controller. Pas de hotfix aan zoals u dat deed voor de vorige controller.
6. Nadat de hotfixes zijn toegepast, sluit u de onderhoudsmodus af. Zie [Stap 4: Onderhoudsmodus afsluiten](../articles/storsimple/storsimple-update-device.md#step4) voor instructies.

