---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176068"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Regelmatige updates installeren via Windows PowerShell voor StorSimple
1. Open de seriële console van het apparaat en selecteer optie 1, **Log in met volledige toegang**. Typ het wachtwoord. Het standaardwachtwoord is *Password1*. 
2. Typ het volgende na de opdrachtprompt:
   
     `Get-HcsUpdateAvailability`
   
    U krijgt een melding of er updates beschikbaar zijn en of de updates storend of niet-storend zijn.
3. Typ het volgende na de opdrachtprompt:
   
     `Start-HcsUpdate`
   
    Het updateproces wordt gestart.

> [!IMPORTANT]
> * Deze opdracht is alleen van toepassing op regelmatige updates. U voert deze opdracht uit op slechts één controller, maar beide controllers worden bijgewerkt. 
> * Mogelijk merkt u dat een controller failover tijdens het updateproces; De failover heeft echter geen invloed op de beschikbaarheid of werking van het systeem.
> 
> 

