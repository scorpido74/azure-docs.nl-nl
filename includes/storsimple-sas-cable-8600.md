---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176069"
---
#### <a name="to-attach-the-sas-cables"></a>De SAS-kabels bevestigen
1. Identificeer de primaire en de EBOD-behuizingen. De twee behuizingen kunnen worden geïdentificeerd door te kijken naar hun respectieve back vliegtuigen. Zie de volgende afbeelding voor begeleiding. 
   
    ![Achtervlak van primaire en EBOD-behuizingen](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Achteraanzicht van primaire en EBOD-behuizingen**
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Primaire behuizing |
   | 2 |EBOD-behuizing |
2. Zoek de serienummers op de primaire en de EBOD-behuizingen. De serienummersticker wordt op het achteroor van elke behuizing aangebracht. De serienummers moeten op beide behuizingen identiek zijn. [Neem](../articles/storsimple/storsimple-contact-microsoft-support.md) onmiddellijk contact op met Microsoft Support als de serienummers niet overeenkomen. Zie de volgende afbeelding om de serienummers te vinden.
   
    ![Achteraanzicht van behuizing met serienummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Locatie van serienummersticker**
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Oor van de behuizing |
3. Gebruik de meegeleverde SAS-kabels om de EBOD-behuizing als volgt op de primaire behuizing aan te sluiten:
   
   1. Identificeer de vier SAS-poorten op de primaire behuizing en de EBOD-behuizing. De SAS-poorten zijn voorzien van het label EBOD op de primaire behuizing en komen overeen met poort A op de EBOD-behuizing, zoals weergegeven in de sas-bekabelingsillustratie hieronder.
   2. Gebruik de meegeleverde SAS-kabels om de EBOD-poort aan te sluiten op poort A.
   3. De EBOD-poort op controller 0 moet worden aangesloten op poort A op EBOD-controller 0. De EBOD-poort op controller 1 moet worden aangesloten op de poort A op EBOD-controller 1. Zie de volgende illustratie voor begeleiding. 
      
      ![SAS-bekabeling voor uw apparaat](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS bekabeling**
      
      | Label | Beschrijving |
      |:--- |:--- |
      | A |Primaire behuizing |
      | B |EBOD-behuizing |
      | 1 |Controller 0 |
      | 2 |Controller 1 |
      | 3 |EBOD-controller 0 |
      | 4 |EBOD-controller 1 |
      | 5, 6 |SAS-poorten op primaire behuizing (met het label EBOD) |
      | 7, 8 |SAS-poorten op EBOD-behuizing (poort A) |

