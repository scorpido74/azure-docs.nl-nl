---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176067"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Reguliere hotfixes installeren via Windows PowerShell voor StorSimple
1. Maak verbinding met de seriële console van het apparaat. Zie [Stap 1: Verbinding maken met de seriële console](../articles/storsimple/storsimple-update-device.md#step1)voor meer informatie.
2. Selecteer in het menu van de seriële console optie 1, **Log in met volledige toegang**. Typ het wachtwoord. Het standaardwachtwoord is **Password1**.
3. Typ het volgende na de opdrachtprompt:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Deze opdracht is alleen van toepassing op gewone hotfixes. U voert deze opdracht uit op slechts één controller, maar beide controllers worden bijgewerkt.
    > Mogelijk merkt u dat een controller failover tijdens het updateproces; De failover heeft echter geen invloed op de beschikbaarheid of werking van het systeem.

4. Lever het pad op de plaats wanneer u daarom wordt gevraagd, aan de gedeelde netwerkmap met de hotfix-bestanden.
5. U wordt gevraagd om een bevestiging. Type **Y** om verder te gaan met de hotfix-installatie.

