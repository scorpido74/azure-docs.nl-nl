---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176071"
---
#### <a name="to-cable-your-device-for-power"></a>Uw apparaat bekabelen voor stroomvoorziening
> [!NOTE]
> Beide behuizingen op uw StorSimple-apparaat bevatten redundante PCM's. Voor elke behuizing moeten de PCM's worden geïnstalleerd en aangesloten op verschillende stroombronnen om een hoge beschikbaarheid te garanderen.
> 
> 

1. Zorg ervoor dat de aan/uit-schakelaars op alle PCM's zich in de UIT-positie bevinden.
2. Sluit op de primaire behuizing de stroomkabels aan op beide PCM's. De netsnoeren zijn in het rood geïdentificeerd in het stroombekabeldiagram, hieronder.
3. Zorg ervoor dat de twee PCM's op de primaire behuizing afzonderlijke stroombronnen gebruiken.
4. Bevestig de netsnoeren aan de stroom op de rackdistributie-eenheden, zoals aangegeven in het stroombekabelingsdiagram.
5. Herhaal stap 2 tot en met 4 voor de EBOD-behuizing.
6. Schakel de EBOD-behuizing in door de aan/uit-schakelaar op elke PCM naar de ON-positie te draaien.
7. Controleer of de EBOD-behuizing is ingeschakeld door te controleren of de groene LED's op de achterkant van de EBOD-controller zijn ingeschakeld.
8. Schakel de primaire behuizing in door elke PCM-schakelaar naar de AAN-positie te draaien.
9. Controleer of het systeem is ingeschakeld door ervoor te zorgen dat de LED's van de apparaatcontroller zijn ingeschakeld.
10. Zorg ervoor dat de verbinding tussen de EBOD-controller en de apparaatcontroller actief is door te controleren of de vier LED's naast de SAS-poort op de EBOD-controller groen zijn.
    
    > [!IMPORTANT]
    > Om een hoge beschikbaarheid voor uw systeem te garanderen, raden we u aan zich strikt te houden aan het stroombekabelingsschema in het volgende diagram.
    > 
    > 
    
    ![Kabel uw 4U-apparaat voor stroom](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Bekabeling**
    
    | Label | Beschrijving |
    |:--- |:--- |
    | 1 |Primaire behuizing |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Controller 0 |
    | 5 |Controller 1 |
    | 6 |EBOD-controller 0 |
    | 7 |EBOD-controller 1 |
    | 8 |EBOD-behuizing |
    | 9 |PDU's |

