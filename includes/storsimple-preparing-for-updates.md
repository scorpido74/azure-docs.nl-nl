---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176228"
---
## <a name="preparing-for-updates"></a>Voorbereiden op updates
U moet de volgende stappen uitvoeren voordat u de update scant en toepast:

1. Maak een cloudmomentopname van de apparaatgegevens.
2. Zorg ervoor dat uw vaste IP's voor de controller routeerbaar zijn en verbinding kunnen maken met internet. Deze vaste IP's worden gebruikt om updates van uw apparaat te onderhouden. U dit testen door de volgende cmdlet op elke controller uit te voeren vanuit de Windows PowerShell-interface van het apparaat:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Voorbeelduitvoer voor Test-Connection wanneer vaste IP's verbinding kunnen maken met internet**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Nadat u deze handmatige voorcontroles hebt voltooid, u doorgaan met het scannen en installeren van de updates.

