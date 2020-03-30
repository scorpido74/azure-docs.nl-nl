---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176062"
---
#### <a name="to-enter-maintenance-mode"></a>De onderhoudsmodus invoeren
1. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**.
2. Typ het wachtwoord. Het standaardwachtwoord is **Password1**.
3. Typ bij de opdrachtprompt
   
     `Enter-HcsMaintenanceMode`
4. U ziet een waarschuwingsbericht waarin staat dat de onderhoudsmodus alle I/O-aanvragen zal verstoren en de verbinding met de klassieke Azure-portal zal verbreken en dat u om bevestiging wordt gevraagd. Typ **Y** om de onderhoudsmodus in te voeren.
   
    Beide controllers worden opnieuw opgestart. Wanneer de herstart is voltooid, verschijnt er een ander bericht dat aangeeft dat het apparaat de onderhoudsmodus heeft.

