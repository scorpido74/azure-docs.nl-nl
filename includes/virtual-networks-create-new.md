---
title: bestand opnemen
description: bestand opnemen
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 43ede7f3340f190f9eb37ca90fb74f2215db9062
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227412"
---
## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en een subnet.

1. Selecteer in de linkerbovenhoek van het scherm **een resource maken > netwerk > virtueel netwerk** of zoek naar **virtueel netwerk** in het zoekvak.

2. In **virtueel netwerk maken**typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Project Details**  |                                                                 |
    | Abonnement     | Uw Azure-abonnement selecteren                                  |
    | Resourcegroep   | Selecteer **Nieuw maken**, Voer **\<resource-group-name >** en selecteer OK. |
    | **Exemplaar Details** |                                                                 |
    | Naam             | Voer **\<virtuele-netwerk naam in >**                                    |
    | Regio           | **\<regio selecteren-naam >** |

3. Selecteer het tabblad **IP-adressen** of selecteer de knop **volgende: IP-adressen** aan de onderkant van de pagina.

4. Voer de volgende gegevens in op het tabblad **IP-adressen** :

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adres ruimte | **\<IPv4-adres ruimte invoeren >** |

5. Selecteer bij **subnetnaam**het woord **standaard**.

6. Voer in **subnet bewerken**de volgende gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Voer **\<subnet naam in >** |
    | Subnetadresbereik | Voer **\<subnet-adres bereik in >**

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **controleren + maken** of selecteer de knop **beoordeling + maken** .

9. Selecteer **Maken**.